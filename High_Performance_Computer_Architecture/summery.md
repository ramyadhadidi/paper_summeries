<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [N1: Overview](#n1-overview)
- [N2: Performance Evaluation](#n2-performance-evaluation)
	- [Laws](#laws)
		- [Amdahl's Law](#amdahls-law)
		- [Gustafson's Law](#gustafsons-law)
		- [Moor's Law](#moors-law)
- [N3: Memory Hierarchies and Caches](#n3-memory-hierarchies-and-caches)
	- [Caches](#caches)
		- [Cache Timing](#cache-timing)
		- [Cache Replacements](#cache-replacements)
		- [Cache Write Strategy](#cache-write-strategy)
		- [Type of Misses](#type-of-misses)
		- [Improving Cache Performance](#improving-cache-performance)
- [N4: Pipelining](#n4-pipelining)
	- [Hazards](#hazards)
		- [Solve Data Hazards](#solve-data-hazards)
		- [Solve Control Hazards](#solve-control-hazards)
- [N5: ILP](#n5-ilp)

<!-- /TOC -->

HPCA Summery

This is summery for cs6290 taught by Tom Conte. I use this to review the most important concepts/terms in architecture.


<!-- --------------------------------------------- -->
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


<!-- --------------------------------------------- -->
# N2: Performance Evaluation

RunTime = IC * CPI * CT
- IC: Instruction Count
- CPI: Cycle per Instruction
- CT: Cycle Time

RISC vs. CISC = (IC>, CPI<, CT<) vs. (IC<, CPI>, CT>)

_Amean_ for runtime / _Hmean_ for rates (IPC)

## Laws
### Amdahl's Law
Speedup is limited by the part you cannot improve
- F: Fraction of original runtime that can be enhanced. 80%?
- S: How much can you parallelized. 100,000?


 - SpeedUp(Ideal) = 1 / (1-F)  ::  1/0.2 = 5
 - SpeedUp = 1 / (1-F) + F/S

### Gustafson's Law
Looks from other perspective, parallel programs. (Must keep CPUs busy)
- a: Fraction of parallel program that is sequential
- P: P processors

- SpeedUp = P - a(P-1)

### Moor's Law
2x Transistors / 18 Month


<!-- --------------------------------------------- -->
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
  - Access TLB in parallel (Virtually indexed, physically tagged)
  - Simple Cache
  - Pipeline Writes


<!-- --------------------------------------------- -->
# N4: Pipelining
Speedup = n / (1+stall)
- n: pipeline depth
- stall: average number of stalls per instruction

## Hazards
- Data
	- RAW, WAR, WAW
- Structural (Hardware resources)
- Control
	- Branch

### Solve Data Hazards
 - Data Forwarding
 - Load-use hazard (load x and the use it, common)

### Solve Control Hazards
- Branch Target Buffer
- 2bit predictor (Smith, biomodal)
- GShare (2Mux one for History, one for PC)
- GSelect (1Mux hash history and PC)
- Yeh/Patt (History table for each PC selects pattern table entry)
- Hybrid (Competition)


<!-- --------------------------------------------- -->
# N5: ILP
 - Flynn's Bottleneck for Pipelining: CPI>=1
 - reduce CT further, but cannot do it forever (Latch overhead, logic)
 - __Scalar__ 1 Inst/Cycle and  __superscalar__ N Inst/Cycle

## SuperScaler
Multiple functional units, multiple ports for caches, parallel I$,
multi ported RegFile, and dependency check hardware


![View of SuperScalar](https://github.com/ramyadhadidi/summeries/blob/master/High_Performance_Computer_Architecture/superscalar.png)

### Approaches for Scheduling
 - Fire (issue) either in order(I) or out of order (O)
 - Complete either in order (I) or other of order (O)
 - Classifications:
 	- FICI (5-stage pipeline)
	- FICO (Cray-1)

#### Simple Interlocking (FICO)
- add register file table (reg, busy, value)
- add scoreboard (FU, busy)
- add scheduling queue (FU, src1, src2, dest)

Continue firing until see a busy. Now we can take care of dependencies while
executing instructions parallel in different FUs.

#### Tomasulo's Algorithm (FOCO)
 - Scheduling queue in dispatch becomes _Reservation Stations_
 - Announcements are made using _Common Data Bus_ (CDB)
 - Use RS and CDB to rename registers (recall IPC ~ #regs)
 - regFile (reg, tag, ready, value)
 	- in dispatch assign tags to dest registers in program order
	- add tag field to RS, CDB and regFile
	- removes WAW and WAR data dependencies

#### RAT
Instead of tags use real registers:
 - Pregs (Actual implemented registers)
 - Aregs (ISA regs)
 - We just save Pregs, we use a table _Register Alias Table_ (RAS) to
 	do the translations

Architecture:
 - RS: FU, Dest AReg, Dest PReg, Src1 PReg, Src2 PReg
 - RAT: AReg -> PReg
 - RegFile: PReg, Pending Free, Free, Ready, #Uses, Value
 - CDB: Just from FUs to RegFile

#### RAT vs. Tomasulo
- no associative lookup in scheduling queue
- no broadcast to listen
- Tomasulo does not check regFile for all sources every cycle

### State Update
We should be able to roll back to a state if something happens. Something
like Fault, Trap or Exception.

#### Solution 0.5: ROB
All tag broadcasts happens at retirements
Issues:
- Head of ROB retirement make all instructions to wait (program order
	retirement)

#### Solution 1: ROB w/bypass
Add comparators to search ROB and therefore retire sooner.

#### Solution 2: Future File
Use two regFiles, one messy, one ROB ordered. This is the common implemented
scheme.

#### Solution 3: Checkpoint Repair
Mark an instruction barrier. We have three regFiles: messy, backup1, backup2
