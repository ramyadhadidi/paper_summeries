# HPCA Summery
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

### Amdahl's Law
Speedup is limited by the part you cannot improve
- F: Fraction of original runtime that can be enhanced? 80%?
- S: How much can you parallelized? 100,000?


 - SpeedUp(Ideal) = 1 / (1-F)  ::  1/0.2 = 5
 - SpeedUp = 1 / (1-F) + F/S
