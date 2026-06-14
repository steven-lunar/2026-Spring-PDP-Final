## Iteration feedback template
```
=== ITERATION FEEDBACK — CG OpenMP, DATA-DRIVEN, Iteration [N] ===

COMPILATION: [SUCCESS / FAILED]
[If FAILED, paste the compiler error log here]

VERIFICATION: [PASS / FAIL]
[If FAIL, Expected Zeta: 0.2897360559285E+02, Actual Zeta: value, Error: value]

=====================================================================
 PERFORMANCE COMPARISON (Class C, 16 OpenMP Threads via numactl Node 0)
=====================================================================
- Baseline wall time:     [X.XX] s
- Previous iteration:     [Y.YY] s  (speedup: [A.AA]x over baseline)
- This iteration:         [Z.ZZ] s  (speedup: [B.BB]x over baseline)
- Improvement this iter:  [+/- C.CC%]

THROUGHPUT METRICS:
- Avg Total Throughput:  [XXXX.XX] Mop/s total

=====================================================================
 HARDWARE COUNTER DRILL-DOWN (Compared to Baseline)
=====================================================================
## CPU Efficiency
- IPC: [X.XXX]  [baseline: 1.236, +/-X.X%]
- task-clock (median): [X.XX] ms  [baseline: 216.10 ms, +/-X.X%]

## Cache (LLC = L3)
- LLC miss rate: [XX.XX] %  [baseline: 26.16 %, +/-X.X%]
- LLC misses per 1K instructions: [XX.XX]  [baseline: 11.13, +/-X.X%]
- Branch miss rate: [X.XX] %  [baseline: 0.47 %, +/-X.X%]

### Raw Counts (median)
- cycles:       [X,XXX,XXX,XXX,XXX]
- instructions: [X,XXX,XXX,XXX,XXX]
- cache-misses: [X,XXX,XXX,XXX,XXX]

## Memory (Socket 0 Local Memory Channels)
- Achieved bandwidth: [XX.XX] GB/s  [baseline: 31.15 GB/s, +/-X.X%]
-   ↳ % of STREAM peak (108.0 GB/s): [XX.X] %  [baseline: 28.8 %, +/-X.X%]
- Data volume:  [XXX.XXXX] GB  [baseline: 577.4816 GB, +/-X.X%]
- Read volume:  [XXX.XXXX] GB  [baseline: 570.3207 GB, +/-X.X%]
- Write volume: [X.XXXX] GB    [baseline: 7.1609 GB, +/-X.X%]

=====================================================================
 SPECIFIC OBSERVATION & ANALYSIS
=====================================================================
[Analyze the metrics change. For example: "The loop fusion in this iteration successfully reduced the Read volume from 570GB to 480GB, leading to an LLC miss rate drop to 21%. However, IPC decreased by 5% due to compiler failing to vectorize the fused loop structure. Verification PASSED."]

=====================================================================
 NEXT ITERATION MANDATE
=====================================================================
Please further optimize the `cg.f90` file. Focus specifically on:
[e.g., "Recovering the lost vectorization efficiency in the fused loops. Ensure that the fused SpMV-reduction structure allows the compiler to generate clean AVX-512 instructions without introducing register spills."]

```

## Iteration 1
```
COMPILATION: FAILED
cg.f90:111:19:

  111 | !$omp end do nowait
      |                   1
Error: Unexpected !$OMP END DO statement at (1)
cg.f90:120:19:

  120 | !$omp end do nowait
      |                   1
Error: Unexpected !$OMP END DO statement at (1)
cg.f90:136:12:

  136 | !$omp end do
      |            1
Error: Unexpected !$OMP END DO statement at (1)
cg.f90:143:19:

  143 | !$omp end do nowait
      |                   1
Error: Unexpected !$OMP END DO statement at (1)
cg.f90:151:21:

  151 | !$omp end parallel do
      |                     1
Error: Unexpected !$OMP END PARALLEL DO statement at (1)
cg.f90:172:12:

  172 | !$omp end do
      |            1
Error: Unexpected !$OMP END DO statement at (1)
cg.f90:191:19:

  191 | !$omp end do nowait
      |                   1
Error: Unexpected !$OMP END DO statement at (1)
cg.f90:238:132:

  238 |       call print_results('CG', class, na, 0, 0, niter, t, mflops, '          floating point', verified, npbversion, compiletime, cs1, cs2, cs3, cs4, cs5, cs6, cs7)
      |                                                                                                                                    1
Error: Line truncated at (1) [-Werror=line-truncation]
cg.f90:238:132:

  238 |       call print_results('CG', class, na, 0, 0, niter, t, mflops, '          floating point', verified, npbversion, compiletime, cs1, cs2, cs3, cs4, cs5, cs6, cs7)
      |                                                                                                                                    1
Error: Syntax error in argument list at (1)
cg.f90:288:12:

  288 | !$omp end do
      |            1
Error: Unexpected !$OMP END DO statement at (1)
cg.f90:294:12:

  294 | !$omp end do
      |            1
Error: Unexpected !$OMP END DO statement at (1)
cg.f90:334:12:

  334 | !$omp end do
      |            1
Error: Unexpected !$OMP END DO statement at (1)
cg.f90:345:12:

  345 | !$omp end do
      |            1
Error: Unexpected !$OMP END DO statement at (1)
cg.f90:365:19:

  365 | !$omp end do nowait
      |                   1
Error: Unexpected !$OMP END DO statement at (1)

VERIFICATION: FAIL

=====================================================================
 PERFORMANCE COMPARISON (Class C, 16 OpenMP Threads via numactl Node 0)
=====================================================================
- Baseline wall time:     N/A s
- Previous iteration:     N/A s  (speedup: N/Ax over baseline)
- This iteration:         N/A s  (speedup: N/Ax over baseline)
- Improvement this iter:  N/A

THROUGHPUT METRICS:
- Avg Total Throughput:  N/A Mop/s total

=====================================================================
 HARDWARE COUNTER DRILL-DOWN (Compared to Baseline)
=====================================================================
## CPU Efficiency
- IPC: N/A  [baseline: 1.236, +/-N/A%]
- task-clock (median): N/A ms  [baseline: 216.10 ms, +/-N/A%]

## Cache (LLC = L3)
- LLC miss rate: N/A %  [baseline: 26.16 %, +/-N/A%]
- LLC misses per 1K instructions: N/A  [baseline: 11.13, +/-N/A%]
- Branch miss rate: N/A %  [baseline: 0.47 %, +/-N/A%]

### Raw Counts (median)
- cycles:       N/A
- instructions: N/A
- cache-misses: N/A

## Memory (Socket 0 Local Memory Channels)
- Achieved bandwidth: N/A GB/s  [baseline: 31.15 GB/s, +/-N/A%]
-   ↳ % of STREAM peak (108.0 GB/s): N/A %  [baseline: 28.8 %, +/-N/A%]
- Data volume:  N/A GB  [baseline: 577.4816 GB, +/-N/A%]
- Read volume:  N/A GB  [baseline: 570.3207 GB, +/-N/A%]
- Write volume: N/A GB    [baseline: 7.1609 GB, +/-N/A%]

=====================================================================
 SPECIFIC OBSERVATION & ANALYSIS
=====================================================================
[Analyze the metrics change. For example: "The loop fusion in this iteration successfully reduced the Read volume from 570GB to 480GB, leading to an LLC miss rate drop to 21%. However, IPC decreased by 5% due to compiler failing to vectorize the fused loop structure. Verification PASSED."]

=====================================================================
 NEXT ITERATION MANDATE
=====================================================================
Please further optimize the `cg.f90` file. Focus specifically on:
[e.g., "Recovering the lost vectorization efficiency in the fused loops. Ensure that the fused SpMV-reduction structure allows the compiler to generate clean AVX-512 instructions without introducing register spills."]
```

## Iteration 2
```
COMPILATION: FAILED
Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Backtrace for this error:

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Backtrace for this error:

Backtrace for this error:

Backtrace for this error:

Backtrace for this error:

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Backtrace for this error:

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Backtrace for this error:

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Backtrace for this error:

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Backtrace for this error:

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Backtrace for this error:

Backtrace for this error:

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Backtrace for this error:

Backtrace for this error:

Backtrace for this error:

Backtrace for this error:

Program received signal SIGSEGV: Segmentation fault - invalid memory reference.

Backtrace for this error:
#0  0x7c96cbe23e59 in ???
#1  0x7c96cbe22e75 in ???
#2  0x7c96cba4532f in ???
#3  0x7c96cbb9943a in ???
#4  0x648e6c1db53f in sparse_
#5  0x648e6c1dc168 in makea_
#6  0x648e6c1dc64d in MAIN__._omp_fn.0
#7  0x7c96cbcd9976 in GOMP_parallel
#8  0x648e6c1da909 in MAIN__
#9  0x648e6c1d924e in main

VERIFICATION: FAIL

=====================================================================
 PERFORMANCE COMPARISON (Class C, 16 OpenMP Threads via numactl Node 0)
=====================================================================
- Baseline wall time:     N/A s
- Previous iteration:     N/A s  (speedup: N/Ax over baseline)
- This iteration:         N/A s  (speedup: N/Ax over baseline)
- Improvement this iter:  N/A

THROUGHPUT METRICS:
- Avg Total Throughput:  N/A Mop/s total

=====================================================================
 HARDWARE COUNTER DRILL-DOWN (Compared to Baseline)
=====================================================================
## CPU Efficiency
- IPC: N/A  [baseline: 1.236, +/-N/A%]
- task-clock (median): N/A ms  [baseline: 216.10 ms, +/-N/A%]

## Cache (LLC = L3)
- LLC miss rate: N/A %  [baseline: 26.16 %, +/-N/A%]
- LLC misses per 1K instructions: N/A  [baseline: 11.13, +/-N/A%]
- Branch miss rate: N/A %  [baseline: 0.47 %, +/-N/A%]

### Raw Counts (median)
- cycles:       N/A
- instructions: N/A
- cache-misses: N/A

## Memory (Socket 0 Local Memory Channels)
- Achieved bandwidth: N/A GB/s  [baseline: 31.15 GB/s, +/-N/A%]
-   ↳ % of STREAM peak (108.0 GB/s): N/A %  [baseline: 28.8 %, +/-N/A%]
- Data volume:  N/A GB  [baseline: 577.4816 GB, +/-N/A%]
- Read volume:  N/A GB  [baseline: 570.3207 GB, +/-N/A%]
- Write volume: N/A GB    [baseline: 7.1609 GB, +/-N/A%]

=====================================================================
 SPECIFIC OBSERVATION & ANALYSIS
=====================================================================
[Analyze the metrics change. For example: "The loop fusion in this iteration successfully reduced the Read volume from 570GB to 480GB, leading to an LLC miss rate drop to 21%. However, IPC decreased by 5% due to compiler failing to vectorize the fused loop structure. Verification PASSED."]

=====================================================================
 NEXT ITERATION MANDATE
=====================================================================
Please further optimize the `cg.f90` file. Focus specifically on:
[e.g., "Recovering the lost vectorization efficiency in the fused loops. Ensure that the fused SpMV-reduction structure allows the compiler to generate clean AVX-512 instructions without introducing register spills."]
```

## Iteration 3
```
COMPILATION: SUCCESS

VERIFICATION: FAIL
   iteration           ||r||                 zeta
        1                        NaN                 NaN
        5                        NaN                 NaN
       10                        NaN                 NaN
       15                        NaN                 NaN
       20                        NaN                 NaN
       25                        NaN                 NaN
       30                        NaN                 NaN
       35                        NaN                 NaN
       40                        NaN                 NaN
       45                        NaN                 NaN
       50                        NaN                 NaN
       55                        NaN                 NaN
       60                        NaN                 NaN
       65                        NaN                 NaN
       70                        NaN                 NaN
       75                        NaN                 NaN

=====================================================================
 PERFORMANCE COMPARISON (Class C, 16 OpenMP Threads via numactl Node 0)
=====================================================================
- Baseline wall time:     N/A s
- Previous iteration:     N/A s  (speedup: N/Ax over baseline)
- This iteration:         N/A s  (speedup: N/Ax over baseline)
- Improvement this iter:  N/A

THROUGHPUT METRICS:
- Avg Total Throughput:  N/A Mop/s total

=====================================================================
 HARDWARE COUNTER DRILL-DOWN (Compared to Baseline)
=====================================================================
## CPU Efficiency
- IPC: N/A  [baseline: 1.236, +/-N/A%]
- task-clock (median): N/A ms  [baseline: 216.10 ms, +/-N/A%]

## Cache (LLC = L3)
- LLC miss rate: N/A %  [baseline: 26.16 %, +/-N/A%]
- LLC misses per 1K instructions: N/A  [baseline: 11.13, +/-N/A%]
- Branch miss rate: N/A %  [baseline: 0.47 %, +/-N/A%]

### Raw Counts (median)
- cycles:       N/A
- instructions: N/A
- cache-misses: N/A

## Memory (Socket 0 Local Memory Channels)
- Achieved bandwidth: N/A GB/s  [baseline: 31.15 GB/s, +/-N/A%]
-   ↳ % of STREAM peak (108.0 GB/s): N/A %  [baseline: 28.8 %, +/-N/A%]
- Data volume:  N/A GB  [baseline: 577.4816 GB, +/-N/A%]
- Read volume:  N/A GB  [baseline: 570.3207 GB, +/-N/A%]
- Write volume: N/A GB    [baseline: 7.1609 GB, +/-N/A%]

=====================================================================
 SPECIFIC OBSERVATION & ANALYSIS
=====================================================================
[Analyze the metrics change. For example: "The loop fusion in this iteration successfully reduced the Read volume from 570GB to 480GB, leading to an LLC miss rate drop to 21%. However, IPC decreased by 5% due to compiler failing to vectorize the fused loop structure. Verification PASSED."]

=====================================================================
 NEXT ITERATION MANDATE
=====================================================================
Please further optimize the `cg.f90` file. Focus specifically on:
[e.g., "Recovering the lost vectorization efficiency in the fused loops. Ensure that the fused SpMV-reduction structure allows the compiler to generate clean AVX-512 instructions without introducing register spills."]
```

## Iteration 4
```
COMPILATION: SUCCESS

VERIFICATION: PASS

=====================================================================
 PERFORMANCE COMPARISON (Class C, 16 OpenMP Threads via numactl Node 0)
=====================================================================
- Baseline wall time:     12.600 s
- This iteration:         12.620 s  (speedup: 1.00x over baseline)
- Improvement this iter:  +0.2%

THROUGHPUT METRICS:
- Avg Total Throughput:  11354.50 Mop/s total

=====================================================================
 HARDWARE COUNTER DRILL-DOWN (Compared to Baseline)
=====================================================================
## CPU Efficiency
- IPC: 1.214  [baseline: 1.236, -1.7%]
- task-clock (median): 218.63 ms  [baseline: 216.10 ms, +1.2%]

## Cache (LLC = L3)
- LLC miss rate: 25.26 %  [baseline: 26.16 %, -3.4%]
- LLC misses per 1K instructions: 11.12  [baseline: 11.13, -0.1%]
- Branch miss rate: 0.48 %  [baseline: 0.47 %, +0.4%]

### Raw Counts (median)
- cycles:       549,365,291,726
- instructions: 666,984,919,594
- cache-misses: 7,413,962,308

## Memory (Socket 0 Local Memory Channels)
- Achieved bandwidth: 31.78 GB/s  [baseline: 31.15 GB/s, +2.0%]
-   ↳ % of STREAM peak (108.0 GB/s): 29.4 %  [baseline: 28.8 %, +2.0%]
- Data volume:  577.5076 GB  [baseline: 577.4816 GB, +0.0%]
- Read volume:  570.2326 GB  [baseline: 570.3207 GB, +0.0%]
- Write volume: 7.2750 GB    [baseline: 7.1609 GB, +1.6%]

=====================================================================
 SPECIFIC OBSERVATION & ANALYSIS
=====================================================================
[Analyze the metrics change. For example: "The loop fusion in this iteration successfully reduced the Read volume from 570GB to 480GB, leading to an LLC miss rate drop to 21%. However, IPC decreased by 5% due to compiler failing to vectorize the fused loop structure. Verification PASSED."]

=====================================================================
 NEXT ITERATION MANDATE
=====================================================================
Please further optimize the `cg.f90` file. Focus specifically on:
[e.g., "Recovering the lost vectorization efficiency in the fused loops. Ensure that the fused SpMV-reduction structure allows the compiler to generate clean AVX-512 instructions without introducing register spills."]

```