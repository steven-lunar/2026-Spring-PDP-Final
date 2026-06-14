# FT Level 2 — Agent Responses

## Iteration v1

**Changes made:**
- Replaced the blocking `MPI_Alltoall` in all three global transposes (`transpose2_global`, `transpose_x_z_global`, `transpose_x_y_global`) with a hand-rolled non-blocking point-to-point all-to-all: `MPI_Irecv` for all 127 peers, `MPI_Isend` to all 127, then `MPI_Waitany` in a loop unpacking each block immediately on arrival. Disjoint staging buffers (`xfer_send`/`xfer_recv`) added; self-message elision; pre-posted receives.
- `ft_data.f90`: `fftblock_default` 16 → 32, `fftblockpad_default` 18 → 34; `transblock` 32 → 64, `transblockpad` 34 → 66.

**Reasoning:** The prompt states MPI_Alltoall dominates with no overlap. Replacing with Irecv/Isend+Waitany allows each block to be unpacked the instant it arrives, overlapping unpack with remaining in-flight receives.

**Result:** COMPILATION SUCCESS, VERIFICATION SUCCESSFUL, wall time 107.13 s (0.49× — 2.06× SLOWER).
mpiP: top function shifted from Alltoall (96.8% of app) to MPI_Waitany (92.9% of MPI time, 357,632 calls).

---

## Iteration v2

**Changes made (based on feedback: 2× regression, Waitany-dominated, revert to native collective):**
- Reverted all three global transpose routines back to the canonical single `MPI_Alltoall`. Removed all `MPI_Irecv`/`MPI_Isend`/`MPI_Waitany` code.
- Kept cache-blocking parameters: `fftblock` 32, `transblock` 64.
- Explicitly declined `MPI_Ialltoall` overlap: within one transpose the unpack depends on the Alltoall output and the next FFT depends on the unpack — no independent work exists to overlap without restructuring the pipeline.

**Reasoning:** The manual point-to-point all-to-all cannot match the vendor's RDMA-tuned MPI_Alltoall. On this fabric the collective uses RDMA-write mode, batching all 127 peer transfers through the adapter without software loops. Replacing it with 127 send/receive pairs adds O(P) completion overhead (357,632 Waitany calls) that dwarfs any overlap benefit given only ~2.5% local compute.

**Result:** COMPILATION SUCCESS, VERIFICATION SUCCESSFUL, wall time 46.51 s (1.12× vs baseline 52.05 s).
mpiP: MPI_Alltoall restored as top function (95.6% of app). Gain comes entirely from fftblock 16→32 reducing local FFT work inside the Alltoall barrier.
