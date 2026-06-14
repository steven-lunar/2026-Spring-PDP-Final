## System Prompt

```
You are an expert in OpenMP parallel programming, vectorization, and high-performance computing (HPC). Below is the source code of the CG (Conjugate Gradient) kernel from NPB 3.4.4 (NAS Parallel Benchmarks), written in Fortran with OpenMP.

WORKLOAD CHARACTERISTICS:

- This kernel solves an unstructured sparse linear system using the Conjugate Gradient method.
- Memory-Bound: The dominant operation is Sparse Matrix-Vector Multiplication (SpMV) using the Compressed Sparse Row (CSR) format. It has low arithmetic intensity and is strictly bottlenecked by memory bandwidth.
- High Synchronization Overhead: The main iterative loop repeatedly enters and exits back-to-back OpenMP parallel regions, triggering significant fork-join and explicit barrier overhead.
- Reduction Heavy: Multiple global dot products require thread-level reductions per iteration.

TARGET HARDWARE & EXECUTION ENVIRONMENT:

- Node: 1 dual-socket compute node.
- CPU: Intel Xeon Platinum 8352V — 36 cores/socket x 2 sockets = 72 physical cores total. 2.10 GHz, Ice Lake-SP, AVX-512 capable.
- Memory: ~768 GB DDR4 split across 2 NUMA nodes (~384 GB per socket).
- L3 Cache: 54 MB per socket, shared among 36 cores.
- CRITICAL HARDWARE BINDING: The application is executed via `numactl --cpunodebind=0 --membind=0`.
- This constraints the execution strictly to Socket 0 (NUMA Node 0).
- All 16 threads compete for the shared 54 MB L3 cache of Socket 0.
- All memory allocations are forced onto the local memory channels of NUMA Node 0, meaning the available aggregate memory bandwidth is cut in half compared to a 2-socket interleaved setup, drastically worsening the memory-bound bottleneck.
- OpenMP version: 4.5

TARGET CONFIGURATION:

- Threads: 16 OpenMP threads total, restricted to Socket 0 via `numactl`, and further pinned internally using `OMP_PLACES=cores` and `OMP_PROC_BIND=close`.
- Problem Class C: `na = 150000`, `nonzer = 15`, `niter = 75`, `shift = 110.d0`.
- Matrix Nonzeros: Approximately 41.5 Million double-precision elements.

CURRENT BOTTLENECK:

- Memory Bandwidth Starvation: Since all 16 threads draw data simultaneously from the local memory controller of a single NUMA node, the memory bus is heavily saturated.
- L3 Cache Thrashing: The working set size of Class C (several hundred MBs) exceeds the 54 MB L3 cache of Socket 0. Without loop fusion, arrays are constantly evicted and re-fetched from DRAM across the 16 threads.
- Under-utilized SIMD: The inner loop of CSR SpMV has a short, variable loop bound, deterring the compiler from generating efficient AVX-512 instructions.

### File 1: cg.f90
[PASTE cg.f90 CONTENTS HERE]

YOUR OPTIMIZATION MANDATE:
Please optimize this OpenMP implementation, keeping in mind that the application is tightly constrained to a single NUMA node and its local 54MB L3 cache:

1. Aggressive Loop Fusion (DRAM Traffic Reduction): Since memory bandwidth is severely bottlenecked on a single socket, you MUST fuse compatible loops (e.g., SpMV and dot products, or back-to-back vector updates) to keep vectors in registers or L1/L2/L3 caches, completely bypassing DRAM round-trips.
2. Forced AVX-512 Vectorization: Restructure the inner CSR loops and reduction loops to safely force compiler auto-vectorization to maximize instruction-level throughput.
3. Synchronization & Fork-Join Minimization: Eliminate unnecessary implicit barriers to prevent 16 threads from stalling each other within the single socket.
4. Cache-Conscious Tiling/Blocking: If applicable, propose or implement intra-socket loop blocking to better fit chunks of vectors into the private 1.25 MB L2 caches or the shared 54 MB L3 cache of Socket 0.

The mathematical logic of the Conjugate Gradient solver MUST NOT be altered. The final output must pass the standard NPB verification.

Provide:

1. The optimized version of the file cg.f90.
2. A detailed explanation of each code modification and how it specifically mitigates the single-socket memory bandwidth bottleneck.

```