
<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:0 orderedList:0 -->

- [N1: Overview](#n1-overview)
- [N2: Performance Evaluation](#n2-performance-evaluation)
  - [Amdahl Law](#amdahl-law)
  - [Gustafson Law](#gustafson-law)
- [N3: Memory Hierarchies and Cache](#n3-memory-hierarchies-and-cache)

<!-- /TOC -->

HPCA Summery

This is summery for cs6290 taught by Tom Conte. I use this to review the most important concepts/terms in architecture.

# N1: Overview
Techniques to speedup:
- Speculation
- Prediction
- Parallelism

Parallelism has different levels: bit level, instruction level, thread level (programmers _pain_), program level

Parallelism can be classified using __Flynn's Taxonomy__. (SISD, SIMD, MISD, MIMD, Also added later: SPMD, SPMT)

Note: Pipelining is neither SIMD nor MIMD

Cray style: Pipeline the execution stage.

Moor's Law: 2X transistor each 18 Month.
  - 1995 stop of exp. transistor improvements
  - 2005 ILP ended with Prescott

# N2: Performance Evaluation

RunTime = IC * CPI * CT
- IC: Instruction Count
- CPI: Cycle per Instruction
- CT: Cycle Time

RISC vs. CISC = (IC>, CPI<, CT<) vs. (IC<, CPI>, CT>)

_Amean_ for runtime / _Hmean_ for rates (IPC)

### Amdahl Law
Speedup is limited by the part you cannot improve
- F: Fraction of original runtime that can be enhanced. 80%?
- S: How much can you parallelized. 100,000?


 - SpeedUp(Ideal) = 1 / (1-F)  ::  1/0.2 = 5
 - SpeedUp = 1 / (1-F) + F/S

### Gustafson Law
Looks from other perspective, parallel programs. (Must keep CPUs busy)
- a: Fraction of parallel program that is sequential
- P: P processors

- SpeedUp = P - a(P-1)

# N3: Memory Hierarchies and Caches
Locality:
- Temporal
- Spatial
- Sequential (Inst$)

## Caches
(C,B,S) = 2^C bytes cache, 2^B bytes blocks, 2^S sets

- Fully Associative
  - Checks all the tags
- Direct Mapped (S=0)
  - Just checks one tag
- Set Associative
  - Check number of sets tags (2^S)

### Cache Timing
AAT = HT + MR * MP

Cache Access Approach: Access Tag and Data in parallel or sequential

### Cache Replacements
 - FIFO
 - LRU
 - LFU
 - Random
 - ... (more on Advanced Memory System Class)

### Cache Write Strategy
- When to Write:
  - Write Back
  - Write Through
- Whether allocate on miss:
  - Write allocate
  - Write no allocate
- Combinations: WB w WA - WT w WNA

### Type of Misses
- Compulsory
- Capacity
- Conflict
- Coherence
- Clairvoyance

### Improving Cache Performance
Improving via: HT, MR or MP
- Reduce Miss Rate
  - Cache:
    - Size
    - Associativity
    - Block Size
  - Victim Cache
  - Prefetching
    - HW
      - +1
      - Strided
      - Markov
  - Data Layout (Inst/Data)
  - etc.
    - Loop Fusion
    - Tiling
    - Loop Interchange
- Reduce Miss Penalty
  - Victim Cache
  - Write Buffers (load/store buffer)
  - Early restart (as soon a the word arrives to computations)
  - Critical word first (Say to memory sytem I need this word first)
  - L2 cache
  - Subblocking (Reduces Tag size)
- Hit Time Reduction
  - Access TLB in parallel
  - Simple Cache
  - Pipeline Writes
