[Same code, workload, and hardware context as Level 2]

PROFILING DATA (from baseline run, class C, 128 MPI processes, median of 4 runs):

=== CPU EFFICIENCY (perf stat) ===
- IPC: 2.52  (high — local FFT compute is already efficient)
- LLC miss rate: 41.41%
- LLC misses per 1K instructions: 0.09
- => Compute and cache are NOT the bottleneck.

=== MPI PROFILE (mpiP) ===
- Total MPI time: 97.5% of wall time
- MPI_Alltoall: 96.8% of total application time (99.5% of all MPI time)
- Call count: 2,816 total (across all 128 ranks x 20 iterations x 4 transposes/iter / 2)
- Message size: ~131 KB per process pair per call (~16.8 MB per rank per call)
- Per-call latency range: 103 ms – 10,700 ms across ranks/calls
  => Large spread indicates ranks blocking/waiting inside the collective (synchronization + load imbalance)

=== TOP MPI CALLSITES ===
- transpose2_global (1-D layout hot path): 20 calls/rank, ~75% of app time, mean ~2.3 s/call, max ~6.0 s/call
- setup/bulk transposes: 2 calls/rank, ~21% of app time, mean ~6.2 s/call, max ~10.4 s/call

=== WALL TIME ===
- Baseline: 52.05 s (median of 4 runs, σ = 1.92 s)
- Mop/s: 7,617

=== ANALYSIS ===
- MPI_Alltoall is called 2,816 times and consumes 97.5% of wall time
- No overlap between computation and communication currently exists
- The 103 ms – 10.7 s per-call spread suggests load imbalance across ranks

Based on this profiling data:
1. Pipeline the 1-D FFT computation with the global all-to-all communication (start Ialltoall, compute partial FFTs on already-available data, then complete the communication)
2. Reduce communication volume or frequency if possible
3. Address the load/wait imbalance in the all-to-all

### File 1: ft_data.f90
[ft_data.f90 contents]

### File 2: ft.f90
[ft.f90 contents]

Generate a fully optimized version of both files. Ensure the code remains correct (NPB verification must PASS).

---

## Iteration feedback template

Results after running your Level 3 optimized code:

COMPILATION: [SUCCESS / FAILED]
VERIFICATION: [PASS / FAIL — if fail, expected checksum vs actual]

PERFORMANCE (class C, 128 procs, median of 4 runs):
- Baseline:          52.05 s  (1.00x)
- This iteration:    [Z.ZZ] s  ([B.BB]x)
- vs previous L3:    [+/- C.CC%]

UPDATED MPI PROFILE:
- MPI time %: [XX]%
- Top function: [name] ([XX]% of app time, [N] calls)

SPECIFIC ISSUE (if any): [describe]

Please refine the optimization.
