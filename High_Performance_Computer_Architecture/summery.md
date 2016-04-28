
<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:1 -->

1. [N1: Overview](#n1-overview)
2. [N2: Performance Evaluation](#n2-performance-evaluation)
		1. [Amdahl Law](#amdahl-law)
		2. [Gustafson Law](#gustafson-law)
3. [N3: Memory Hierarchies and Cache](#n3-memory-hierarchies-and-cache)

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

# N3: Memory Hierarchies and Cache
