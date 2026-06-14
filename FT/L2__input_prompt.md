Below is the source code of the FT (3-D Fast Fourier Transform) kernel from NPB 3.4.4 (NAS Parallel Benchmarks), written in Fortran with MPI.

WORKLOAD CHARACTERISTICS:
- This kernel performs a 3-D distributed FFT using a pencil decomposition
- The dominant operation is MPI_Alltoall for global matrix transposition
- MPI_Alltoall accounts for approximately 60-70% of total wall time
- Each FFT direction requires one global all-to-all transpose (2 transposes per forward FFT, 2 per inverse FFT = 4 total per iteration)
- The local FFT computation (1-D Cooley-Tukey) is relatively fast
- This benchmark is communication-bound at scale

TARGET HARDWARE:
- Nodes: 4 compute nodes, connected by a high-speed RDMA fabric (InfiniBand-class), accessed through UCX (rc transport)
- CPU per node: Intel Xeon Platinum 8352V — 36 cores/socket x 2 sockets = 72 physical cores per node (144 logical with hyper-threading), 2.10 GHz, Ice Lake-SP, AVX-512 capable
- Memory per node: ~768 GB DDR4 across 2 NUMA nodes (~384 GB per socket)
- L3 cache: 54 MB per socket (108 MB per node)
- L2 cache: 1.25 MB per core
- Compiler: GNU Fortran 13.3.0 with -O3 -march=native
- MPI: Open MPI 4.1.6 over UCX

TARGET CONFIGURATION:
- 128 MPI processes total (4 nodes x 32 processes/node)
- Problem class C: 512 x 512 x 512 complex doubles, 20 iterations
- Layout: NPB FT 2D pencil decomposition (process grid np1 x np2)

CURRENT BOTTLENECK:
- transpose_x_z_global and transpose_x_y_global use blocking MPI_Alltoall
- All processes must wait for the global communication to complete before proceeding to the next FFT stage
- No overlap between communication and computation currently exists

### File 1: ft_data.f90
[ft_data.f90 contents]

### File 2: ft.f90
[ft.f90 contents]

Please optimize this code with focus on:
1. Replacing blocking MPI_Alltoall with non-blocking MPI_Ialltoall to enable computation-communication overlap
2. Tuning cache blocking parameters (fftblock, transblock) for the 54 MB L3 cache
3. Overlapping local transpose packing/unpacking with MPI communication where possible
4. Any other communication-aware optimizations specific to this pattern

Provide:
1. Complete optimized versions of both files
2. Explanation of each change and the expected impact
3. Which changes address the communication bottleneck specifically

---

## Iteration feedback template

Here are the results of running your Level 2 optimized code:

COMPILATION: [SUCCESS / FAILED]
VERIFICATION: [PASS / FAIL]

PERFORMANCE COMPARISON (class C, 128 MPI processes, median of 4 runs):
- Baseline wall time:     [X.XX] s
- Previous iteration:     [Y.YY] s  (speedup: [A.AA]x over baseline)
- This iteration:         [Z.ZZ] s  (speedup: [B.BB]x over baseline)
- Improvement this iter:  [+/- C.CC%]

MPI PROFILE (mpiP):
- MPI time %: [XX]%
- Top MPI function: [name] ([XX]% of app time)

Please further optimize. Focus specifically on [remaining bottleneck].
If MPI_Ialltoall is being used, ensure that useful computation is actually happening between the Ialltoall call and the Wait call.
