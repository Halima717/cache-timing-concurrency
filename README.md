## 1. Problem Statement and Justification

Modern processors use cache memory to improve performance. Differences in cache access times can reveal information about another program’s memory activity — a *cache side-channel attack*. This project investigates how **CPU scheduling and concurrent workloads** affect the reliability of the **Flush+Reload** cache side-channel technique on Linux.

**Computational justification**: The experiment requires collecting and analysing a large number of cache-timing measurements under multiple workload conditions (baseline, CPU-intensive, memory-intensive). Repeated trials, multiple thread/process configurations, and parallel data processing make this workload **inherently parallelisable** and suitable for a Parallel and Distributed Computing study.

**Main research question**:  
> How do CPU scheduling conditions and concurrent workloads affect the reliability of Flush+Reload cache-timing observations on Linux?

**Hypothesis**: Memory-intensive workloads will degrade Flush+Reload accuracy more than CPU-intensive workloads due to L3 cache contention. Timing variance is expected to increase with thread count, with a corresponding decrease in detection accuracy. The P-core vs. E-core comparison may reveal different noise profiles.

---

## 2. High-Level Sequential Algorithm

The baseline implementation performs the experiment **without parallel workload generation**:

1. Configure experimental parameters.
2. Start a controlled victim program that accesses a shared memory location according to a known pattern.
3. Run an observer program to collect cache-timing measurements.
4. Repeat the experiment under **baseline conditions**.
5. Repeat under **CPU-intensive** and **memory-intensive** workloads, executed sequentially.
6. Store the collected measurements.
7. Calculate timing variation and detection accuracy.
8. Compare results across different conditions.

This sequential version serves as the **correctness and performance baseline** for all parallel implementations.

---

## 3. Planned Parallelization Strategies

### A. OpenMP — Shared-Memory Parallelism

- Generate concurrent CPU-intensive and memory-intensive workloads using multiple threads.
- Parallelise independent batches of measurement-data processing.
- Compare execution using different thread counts (1, 2, 4, 8, up to 12).
- Study how increased workload affects cache-timing observations.

### B. MPI — Distributed-Memory Parallelism

- Divide independent experimental batches among MPI processes.
- Each process performs a separate set of controlled experiments.
- Collect results from different processes using MPI communication.
- Aggregate measurements at the main process for analysis.

### Comparison Metrics

OpenMP and MPI will be compared in terms of **execution time, speedup, efficiency, and overhead**. The sequential implementation provides the baseline for all comparisons.

---

## 4. Target Hardware and Expected Experiment Scale

| Component          | Specification                        |
|--------------------|--------------------------------------|
| Processor          | Intel Core 5 120U                    |
| Physical cores     | 10                                   |
| Logical processors | 12                                   |
| L3 cache           | 12 MB                                |
| Operating system   | Ubuntu Linux                         |
| Language           | C/C++                                |
| Parallel tech.     | OpenMP and MPI                       |
| Compiler           | GCC                                  |
| Analysis           | Python and Matplotlib                |

**Planned experiments**:
- Sequential baseline.
- OpenMP with 1, 2, 4, 8, and up to 12 threads.
- MPI with different process counts.
- Baseline, CPU-intensive, and memory-intensive workloads.
- Repeated timing measurements for statistical reliability.

**Initial target**: At least **1,000 observations per experimental configuration**, subject to pilot testing and hardware feasibility. **CPU affinity** (via `taskset` or `sched_setaffinity`) will be used to pin the observer and victim to specific cores.

---

## 5. Repository Structure and Development Strategy

### Branch Strategy

| Branch                  | Purpose                              |
|-------------------------|--------------------------------------|
| `main`                  | Stable and tested code               |
| `feature/sequential-baseline` | Sequential implementation       |
| `feature/openmp`        | OpenMP implementation                |
| `feature/mpi`           | MPI implementation                   |
| `feature/analysis`      | Data processing and visualisation    |

### Tag Strategy

| Tag               | Milestone                        |
|-------------------|----------------------------------|
| `v0.1-scope`      | Project scope finalised          |
| `v0.2-sequential` | Sequential baseline complete     |
| `v0.3-openmp`     | OpenMP implementation complete   |
| `v0.4-mpi`        | MPI implementation complete      |
| `v1.0-final`      | Final submission                 |

### Repository Contents (Planned)

- `src/` — C/C++ source files (victim, observer, workloads).
- `scripts/` — Build and experiment automation.
- `analysis/` — Python scripts for data processing and plots.
- `results/` — Measurement data (organised by experiment configuration).
- `docs/` — Additional documentation and references.

Progressive commits will be made throughout development to reflect the branch and tag strategy above.

