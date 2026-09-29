# Multithreaded Programming Using Pthreads and OpenMP

## Executive Summary

This experiment demonstrates multithreaded programming in C using **POSIX Threads (Pthreads)** and **OpenMP**. The experiment progresses from basic thread creation to work distribution, race-condition demonstration, synchronization, thread coordination, and performance analysis.

The final performance experiment evaluates the same computational workload using:

1. Sequential execution
2. Pthreads with different numbers of threads
3. OpenMP with different numbers of threads

The measured results show that increasing the number of threads substantially reduces execution time for this workload. The sequential baseline measured in this execution was **3.346414 seconds**. With 16 threads, Pthreads completed the workload in **0.530415 seconds**, while OpenMP completed it in **0.448971 seconds**.

The experiment also demonstrates that speedup is not perfectly linear because parallel execution introduces overhead such as thread management, scheduling, synchronization, memory access, operating-system activity, and non-parallel work.

---

## Table of Contents

- [Executive Summary](#executive-summary)
- [Project Objectives](#project-objectives)
- [Experiment Overview](#experiment-overview)
- [Software Environment](#software-environment)
- [Architecture and Execution Flow](#architecture-and-execution-flow)
- [Concepts Covered](#concepts-covered)
- [Implemented Programs](#implemented-programs)
- [Project Structure](#project-structure)
- [Execution Steps](#execution-steps)
  - [Start WSL](#1-start-wsl)
  - [Create the Lab Directory](#2-create-the-lab-directory)
  - [Check GCC](#3-check-gcc)
  - [Check OpenMP Support](#4-check-openmp-support)
  - [Compile and Run Sequential Program](#5-compile-and-run-sequential-program)
  - [Compile and Run Pthreads Performance Program](#6-compile-and-run-pthreads-performance-program)
  - [Compile and Run OpenMP Performance Program](#7-compile-and-run-openmp-performance-program)
- [Performance Analysis](#performance-analysis)
  - [Execution Time](#execution-time)
  - [Speedup](#speedup)
  - [Efficiency](#efficiency)
  - [Pthreads vs OpenMP](#pthreads-vs-openmp)
- [Race Conditions and Synchronization](#race-conditions-and-synchronization)
- [Pthreads vs OpenMP](#pthreads-vs-openmp-1)
- [Key Observations](#key-observations)
- [Conclusion](#conclusion)

---

## Project Objectives

The objectives of this experiment are to:

- Understand the concept of threads and multithreading.
- Create and manage threads using Pthreads.
- Create parallel regions using OpenMP.
- Distribute work among multiple threads.
- Understand shared data and race conditions.
- Apply synchronization using Pthread mutexes.
- Apply synchronization using OpenMP critical sections.
- Coordinate threads using OpenMP barriers.
- Combine partial results using OpenMP reduction.
- Measure execution time for sequential and parallel programs.
- Calculate speedup and efficiency.
- Compare Pthreads and OpenMP for the same computational workload.
- Understand why parallel speedup is not perfectly linear.

---

## Experiment Overview

A thread is an execution path inside a program. In sequential execution, one thread performs the complete workload. In multithreaded execution, the workload can be divided among multiple threads so that different parts can be processed concurrently.

The experiment uses two parallel programming technologies:

- **Pthreads** — explicit thread creation and management.
- **OpenMP** — directive-based parallel programming with runtime thread management.

The experiment follows this learning flow:

```text
Understand Threads
       |
       v
Create One Thread
       |
       v
Create Multiple Threads
       |
       v
Divide Work
       |
       v
Shared Data
       |
       v
Race Condition
       |
       v
Synchronization
       |
       v
OpenMP Parallel Region
       |
       v
OpenMP Work Sharing
       |
       v
OpenMP Synchronization
       |
       v
Sequential Performance
       |
       v
Pthreads Performance
       |
       v
OpenMP Performance
       |
       v
Execution-Time Analysis
       |
       v
Speedup
       |
       v
Efficiency
       |
       v
Final Analysis
```

---

## Software Environment

The experiment is designed for:

| Component | Technology |
|---|---|
| Host OS | Windows |
| Linux Environment | WSL Ubuntu |
| Programming Language | C |
| Compiler | GCC |
| Thread Library | POSIX Threads (Pthreads) |
| Parallel Library | OpenMP |
| Text Editor | Nano |
| Execution Environment | Linux terminal through WSL |

---

## Architecture and Execution Flow

The overall architecture of the experiment can be represented as follows:

```text
                    Multithreaded C Experiment
                              |
             +----------------+----------------+
             |                                 |
             v                                 v
       Sequential                         Parallel
       Program                                |
             |                    +-----------+-----------+
             |                    |                       |
             v                    v                       v
       Single Thread          Pthreads                OpenMP
                                  |                       |
                                  v                       v
                         pthread_create()          #pragma omp
                         pthread_join()             parallel
                         Mutex                     parallel for
                                  |                 reduction
                                  |                 critical
                                  |                 barrier
                                  |                       |
                                  +-----------+-----------+
                                              |
                                              v
                                      Performance Timing
                                              |
                                              v
                              +---------------+---------------+
                              |               |               |
                              v               v               v
                        Execution Time     Speedup       Efficiency
```

### Performance Workflow

```text
Same computational workload
            |
            +------------------+
            |                  |
            v                  v
      Sequential          Parallel versions
                              |
                    +---------+---------+
                    |                   |
                    v                   v
                 Pthreads            OpenMP
                    |                   |
                    +---------+---------+
                              |
                              v
                       Measure Time
                              |
                              v
                    Compare with Baseline
                              |
                              v
                    Calculate Speedup
                              |
                              v
                    Calculate Efficiency
```

---

## Concepts Covered

### 1. Pthreads

Pthreads stands for POSIX Threads. It provides explicit control over thread creation and management.

Important functions used in the experiment include:

- `pthread_create()` — creates a new thread.
- `pthread_join()` — waits for a thread to finish.
- `pthread_mutex_lock()` — locks a critical section.
- `pthread_mutex_unlock()` — releases the mutex.

### 2. OpenMP

OpenMP provides a higher-level approach to parallel programming using compiler directives.

Important constructs used include:

```c
#pragma omp parallel
#pragma omp parallel for
#pragma omp critical
#pragma omp barrier
```

The experiment also uses:

```c
reduction(+:total_sum)
```

to safely combine partial results.

### 3. Work Distribution

A large task can be divided into smaller pieces and assigned to different threads.

For example:

```text
Large workload
      |
      +---- Thread 1 -> Part 1
      +---- Thread 2 -> Part 2
      +---- Thread 3 -> Part 3
      +---- Thread 4 -> Part 4
```

### 4. Race Condition

A race condition can occur when multiple threads access or modify shared data without proper coordination.

For example:

```c
counter++;
```

When several threads execute this operation concurrently, updates can be lost.

### 5. Synchronization

Synchronization protects shared data and coordinates thread execution.

Pthreads uses a mutex:

```c
pthread_mutex_lock(&mutex);
counter++;
pthread_mutex_unlock(&mutex);
```

OpenMP uses a critical section:

```c
#pragma omp critical
{
    counter++;
}
```

### 6. Barrier

An OpenMP barrier ensures that threads reach a common synchronization point before continuing.

```c
#pragma omp barrier
```

---

## Implemented Programs

### Part A — Pthreads

| Program | Purpose |
|---|---|
| `thread1.c` | Create one thread |
| `thread2.c` | Create multiple threads |
| `thread_sum.c` | Divide work among threads |
| `race.c` | Demonstrate a race condition |
| `mutex.c` | Fix a race condition using a mutex |
| `pthread_perf.c` | Measure Pthreads performance |

### Part B — OpenMP

| Program | Purpose |
|---|---|
| `omp1.c` | Parallel region and thread identification |
| `omp_sum.c` | Work sharing and reduction |
| `omp_race.c` | Demonstrate a race condition |
| `omp_critical.c` | Synchronization using critical section |
| `omp_barrier.c` | Thread coordination |
| `omp_perf.c` | Measure OpenMP performance |

### Part C — Performance Analysis

The performance section compares:

- Sequential execution
- Pthreads execution
- OpenMP execution
- Execution time
- Speedup
- Efficiency
- Performance as thread count increases

---

## Project Structure

A suitable repository structure is:

```text
parallel_lab/
|
├── sequential.c
|
├── thread1.c
├── thread2.c
├── thread_sum.c
├── race.c
├── mutex.c
├── pthread_perf.c
|
├── omp1.c
├── omp_sum.c
├── omp_race.c
├── omp_critical.c
├── omp_barrier.c
├── omp_perf.c
|
└── README.md
```

---

# Execution Steps

## 1. Start WSL

Open Windows PowerShell and start WSL:

```bash
wsl
```

You should enter the Ubuntu Linux environment.

---

## 2. Create the Lab Directory

Create the directory:

```bash
mkdir -p ~/parallel_lab
```

Move into it:

```bash
cd ~/parallel_lab
```

Verify the current directory:

```bash
pwd
```

---

## 3. Check GCC

Check that GCC is installed:

```bash
gcc --version
```

---

## 4. Check OpenMP Support

Run:

```bash
gcc -fopenmp --version
```

The `-fopenmp` compiler option enables OpenMP support while compiling OpenMP programs.

---

## 5. Compile and Run Sequential Program

Create or open the source file:

```bash
nano sequential.c
```

Compile:

```bash
gcc sequential.c -o sequential
```

Run:

```bash
./sequential
```

The workload used in the experiment is:

```c
#define N 1000000000L
```

The program calculates:

```c
sum += (double)i * 0.000001;
```

Expected result:

```text
Result = 499999999500.00
```

Measured execution time in this experiment:

```text
3.346414 seconds
```

This value is used as the sequential baseline for the performance analysis.

---

## 6. Compile and Run Pthreads Performance Program

Create or open:

```bash
nano pthread_perf.c
```

Compile:

```bash
gcc pthread_perf.c -o pthread_perf -pthread
```

Run:

```bash
./pthread_perf
```

Enter the desired number of threads when prompted.

The measured thread counts were:

```text
1
2
4
6
16
```

Example:

```text
Enter number of threads: 4
```

The program divides the range from `0` to `N` among the requested number of threads and combines their partial sums.

---

## 7. Compile and Run OpenMP Performance Program

Create or open:

```bash
nano omp_perf.c
```

Compile:

```bash
gcc omp_perf.c -o omp_perf -fopenmp
```

Run:

```bash
./omp_perf
```

Enter:

```text
1
```

Then repeat the execution for:

```text
2
4
6
16
```

The OpenMP program uses:

```c
omp_set_num_threads(num_threads);

#pragma omp parallel for reduction(+:sum)
```

The reduction operation safely combines the partial sums calculated by the threads.

---

# Performance Analysis

## Experimental Workload

The same workload was used for sequential, Pthreads, and OpenMP implementations.

```text
N = 1,000,000,000 iterations
```

The calculated result remained:

```text
499999999500.00
```

for the measured runs.

This provides a common workload for comparing execution times.

---

## Execution Time

### Sequential Baseline

| Implementation | Threads | Execution Time |
|---|---:|---:|
| Sequential | 1 | **3.346414 s** |

The sequential execution is used as the baseline:

```text
Baseline = 3.346414 seconds
```

### Pthreads vs OpenMP

The following values are taken from the measured terminal executions for this experiment.

| Threads | Pthreads Time (s) | OpenMP Time (s) |
|---:|---:|---:|
| 1 | 3.205682 | 3.186103 |
| 2 | 1.689293 | 1.662809 |
| 4 | 0.947512 | 0.978149 |
| 6 | 0.850071 | 0.841213 |
| 16 | 0.530415 | 0.448971 |

### Execution-Time Interpretation

As the thread count increases, execution time decreases for both Pthreads and OpenMP in these measurements.

For example:

- Pthreads: `3.205682 s` with 1 thread -> `0.530415 s` with 16 threads.
- OpenMP: `3.186103 s` with 1 thread -> `0.448971 s` with 16 threads.

The 16-thread runs therefore require substantially less time than the corresponding 1-thread parallel runs.

---
<img width="1117" height="664" alt="graph1(exec_vs_noofthreads)" src="https://github.com/user-attachments/assets/e7e0d60d-111f-4b56-bb01-cbd3947d9ed9" />


## Speedup

Speedup is calculated relative to the sequential baseline:

```text
Speedup = Sequential Execution Time / Parallel Execution Time
```

Using the measured sequential baseline:

```text
Sequential = 3.346414 seconds
```

### Pthreads Speedup

| Threads | Time (s) | Speedup |
|---:|---:|---:|
| 1 | 3.205682 | 1.044x |
| 2 | 1.689293 | 1.981x |
| 4 | 0.947512 | 3.532x |
| 6 | 0.850071 | 3.937x |
| 16 | 0.530415 | 6.309x |

### OpenMP Speedup

| Threads | Time (s) | Speedup |
|---:|---:|---:|
| 1 | 3.186103 | 1.050x |
| 2 | 1.662809 | 2.013x |
| 4 | 0.978149 | 3.421x |
| 6 | 0.841213 | 3.978x |
| 16 | 0.448971 | 7.454x |

At 16 threads:

- Pthreads speedup = **6.309x**
- OpenMP speedup = **7.454x**

These values are based on the actual execution times shown in the provided terminal results.

---
<img width="1014" height="604" alt="graph2_speed_vs_noofthreads" src="https://github.com/user-attachments/assets/fcc4e7ae-aa9d-41ab-9344-a921c9a19a1c" />


## Efficiency

Efficiency is calculated as:

```text
Efficiency = Speedup / Number of Threads × 100
```

### Pthreads Efficiency

| Threads | Speedup | Efficiency |
|---:|---:|---:|
| 1 | 1.044x | 104.39% |
| 2 | 1.981x | 99.05% |
| 4 | 3.532x | 88.29% |
| 6 | 3.937x | 65.61% |
| 16 | 6.309x | 39.43% |

### OpenMP Efficiency

| Threads | Speedup | Efficiency |
|---:|---:|---:|
| 1 | 1.050x | 105.03% |
| 2 | 2.013x | 100.63% |
| 4 | 3.421x | 85.53% |
| 6 | 3.978x | 66.30% |
| 16 | 7.454x | 46.58% |

At 16 threads, the measured efficiency is:

- Pthreads: **39.43%**
- OpenMP: **46.58%**

The efficiency decreases as the thread count becomes large relative to the amount of useful work each thread performs. Parallel overhead prevents speedup from increasing perfectly proportionally with the number of threads.

---
<img width="999" height="592" alt="graph3_efficiency_vs_noofthreads" src="https://github.com/user-attachments/assets/9c4a1738-1999-4d79-907c-94d08821ef80" />


## Pthreads vs OpenMP

| Aspect | Pthreads | OpenMP |
|---|---|---|
| Thread creation | `pthread_create()` | OpenMP runtime |
| Thread completion | `pthread_join()` | End of parallel region |
| Work distribution | Explicitly managed by programmer | `parallel for` can distribute loop iterations |
| Shared-data protection | Mutex | `critical` |
| Coordination | Join and synchronization mechanisms | `barrier` |
| Partial-result combination | Programmer-managed | `reduction` |
| Programming level | Lower-level, explicit control | Higher-level, directive-based |

The experiment shows that both approaches can effectively parallelize the workload.

---

# Race Conditions and Synchronization

## Pthreads Race Condition

The Pthreads race-condition experiment uses:

```c
counter++;
```

with multiple threads accessing the same shared variable.

The expected result is:

```text
400000
```

but an unprotected shared update can produce an incorrect value because multiple threads may read and write the variable concurrently.

### Pthreads Solution

A mutex protects the critical section:

```c
pthread_mutex_lock(&mutex);

counter++;

pthread_mutex_unlock(&mutex);
```

This restricts access to the protected operation so that only one thread executes it at a time.

---

## OpenMP Race Condition

OpenMP can also experience race conditions when multiple threads update shared data.

The problematic operation is:

```c
counter++;
```

### OpenMP Solution

The operation can be protected using:

```c
#pragma omp critical
{
    counter++;
}
```

Only one OpenMP thread can execute the critical section at a time.

---

## OpenMP Reduction

For the performance program, the sum is combined using:

```c
#pragma omp parallel for reduction(+:sum)
```

Conceptually:

```text
Thread 1 -> Partial Sum
Thread 2 -> Partial Sum
Thread 3 -> Partial Sum
Thread 4 -> Partial Sum
       |
       v
OpenMP combines partial results
       |
       v
Total Sum
```

This avoids an unsafe shared update to `sum`.

---

# Key Observations

1. Multiple threads can reduce execution time for a suitable computational workload.

2. Both Pthreads and OpenMP showed substantial reductions in execution time as the thread count increased.

3. The measured sequential baseline was **3.346414 seconds**.

4. The 16-thread Pthreads execution took **0.530415 seconds**.

5. The 16-thread OpenMP execution took **0.448971 seconds**.

6. Based on the sequential baseline, the measured 16-thread speedups were approximately **6.31x for Pthreads** and **7.45x for OpenMP**.

7. Speedup is not perfectly linear because parallel execution introduces overhead.

8. Relevant sources of overhead include:
   - Thread management
   - Scheduling
   - Synchronization
   - Memory access
   - Operating-system activity
   - Non-parallel work

9. Race conditions demonstrate that parallel execution does not automatically make shared-data operations safe.

10. Synchronization mechanisms such as mutexes and critical sections are required when shared data must be protected.

---

# Conclusion

This experiment demonstrates the development and analysis of multithreaded C programs using Pthreads and OpenMP.

Pthreads provides explicit control over thread creation, joining, work distribution, and mutex-based synchronization. OpenMP provides a higher-level programming model using parallel regions, work-sharing directives, critical sections, barriers, and reductions.

The performance experiment used the same computational workload for sequential, Pthreads, and OpenMP implementations. The measured results show that increasing the number of threads reduced execution time for this workload.

The final measurements were:

```text
Sequential : 3.346414 s
Pthreads 16 threads : 0.530415 s
OpenMP   16 threads : 0.448971 s
```

The experiment also demonstrates that additional threads do not produce perfectly proportional speedup because parallel execution introduces overhead.

Overall learning flow:

```text
Create
  ->
Manage
  ->
Divide Work
  ->
Share Data
  ->
Handle Race Conditions
  ->
Synchronize
  ->
Coordinate
  ->
Measure Performance
  ->
Analyze Results
```

---

