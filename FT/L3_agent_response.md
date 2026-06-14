# FT Level 3 — Agent Responses

## Iteration v1

**Changes made:**
- Replaced `MPI_Alltoall` in all three global transposes with a hand-rolled non-blocking pairwise all-to-all: `MPI_Irecv`/`MPI_Isend` per peer, `MPI_Waitany`/`Waitall`, unpacking each peer block on arrival.
- **Profiling-specific refinements:**
  - Rotating pairwise schedule (`dst = (me+k) mod P`, `src = (me-k) mod P`) to avoid the incast hot-spot causing the 103 ms–10.7 s latency spread.
  - Self-block elision: unpack own block directly from send buffer, removing one in-flight message per transpose (reduces volume by 1/P).
  - Dedicated disjoint `xsend` send buffer to allow safe overlap of unpack with active sends.

**Reasoning:** The profiling shows 97.5% MPI time and a large per-call latency spread (103 ms – 10.7 s), suggesting both communication dominance and incast-induced imbalance. A rotating schedule was expected to distribute traffic more evenly across the fabric.

**Result:** COMPILATION SUCCESS, VERIFICATION SUCCESSFUL, wall time ~102 s (0.51× — ~2× SLOWER).
mpiP: MPI_Wait at 97.4% of app time across 21,504 calls. The rotating schedule did not reduce the imbalance; on RDMA the latency spread reflects the collective's synchronization barrier (slowest rank sets completion time), not incast congestion.

---

## Iteration v2

**Changes made (based on feedback: revert to native collective):**
- Reverted all three global transposes to canonical `MPI_Alltoall`. Removed the hand-rolled pairwise exchange and `xsend` buffer.
- Did NOT add cache-blocking parameter changes (kept `fftblock` 16, `transblock` 32) — remained focused on communication.
- Proposed slab-pipelined `MPI_Ialltoall` as the next step but declined to implement it without measurement.

**Result:** COMPILATION SUCCESS, VERIFICATION SUCCESSFUL, wall time ≈ 52.05 s (1.00× — back to baseline).

---

## Iteration v3

**Changes made (slab-pipelined MPI_Ialltoall, model's own proposal):**
- Split the dominant `transpose2_global` all-to-all into `n_chunks = 2` contiguous bands per process.
- Each band exchanged via `MPI_Ialltoall` with a resized contiguous datatype; post band k+1 while unpacking band k (overlap of local unpack with in-flight communication). At most 2 non-blocking collectives outstanding.
- Added dedicated `tsend` module send buffer for safe overlap (send source, recv buffer, and output are three disjoint arrays).
- Bit-exact data routing: union of slabs equals the original single MPI_Alltoall.
- Applied only to `transpose2_*` (the 1-D-layout hot path for class C at 128 ranks); 2-D-layout paths left unchanged.

**Reasoning:** The profiling showed no overlap between computation and communication. Splitting the all-to-all into two bands keeps the vendor collective (avoiding the O(P) software overhead from v1) while overlapping the per-band unpack with the next band's transfer. The profiling-observed 131 KB per peer was expected to be large enough to stay in the efficient RDMA large-message path even when halved.

**Result:** COMPILATION SUCCESS, VERIFICATION SUCCESSFUL, wall time 93.84 s (0.56× — 1.79× SLOWER, confirmed genuine via interleaved measurement).
mpiP: MPI_Wait at 93.3% of app time across 5,632 calls. Halving the message size from ~131 KB to ~65 KB per peer pushed the exchange below the RDMA adapter's large-message algorithm threshold; the collective fell back to a less efficient protocol, and the algorithm-switch overhead outweighed the overlap benefit.
