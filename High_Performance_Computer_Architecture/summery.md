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
	- [SuperScaler](#superscaler)
		- [Approaches for Scheduling](#approaches-for-scheduling)
			- [Simple Interlocking (FICO)](#simple-interlocking-fico)
			- [Tomasulo's Algorithm (FOCO)](#tomasulos-algorithm-foco)
			- [RAT](#rat)
			- [RAT vs. Tomasulo](#rat-vs-tomasulo)
		- [State Update](#state-update)
			- [Solution 0.5: ROB](#solution-05-rob)
			- [Solution 1: ROB w/bypass](#solution-1-rob-wbypass)
			- [Solution 2: Future File](#solution-2-future-file)
			- [Solution 3: Checkpoint Repair](#solution-3-checkpoint-repair)
	- [VLIW](#vliw)
- [N6: Multiprocessing and Multithreading](#n6-multiprocessing-and-multithreading)
	- [Shared Memory](#shared-memory)
		- [Schemes](#schemes)
			- [Snoopy Protocols](#snoopy-protocols)

<!-- /TOC -->

-----------------------------------------------------

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

## VLIW
Perform dependence check offline (compilers) and then schedule instructions
in _very long instruction words (VLIW)_. We could end up in NOP (vertical
and horizontal). Use stop bit to end horizontal VLIWs, use pause bit to show
how many cycles to wait for vertical nops.

Compilers play central role in scheduling of instructions. There are
multiple compiler techniques. (Global, local, cyclic, acyclic)

We can do cyclic scheduling using Iterative Modulo Scheduling by finding
_ResMII_ and _RecMII_. More on slides.

# N6: Multiprocessing and Multithreading
A SPMD (single program single data) in Flynn's taxonomy.

Interconnect:
- Bus
- Point to point

Programmer's memory:
- Shared memory (coherence)
- Non-shared (message passing)

## Shared Memory
Since we have one memory and multiple private caches, stale values can
happen. Coherence means:
- A processor can read what it wrote
- All write are globally seen eventually
- All writes are ordered globally in the system

### Schemes
 1. Shared cache: not scalable
 2. Snooping: need broadcast network
 3. Directory-based: one place to keep sharing state, works with point to point networks

#### Snoopy Protocols
Bus Actions:
	- GetS: Read Miss, intent is to share
	- GetM: Read, may modify

Coherence Write Policies:
	- Write-Update
	- Write-Invalidate

![States](https://github.com/ramyadhadidi/summeries/blob/master/High_Performance_Computer_Architecture/states.png)
![Coh1](https://github.com/ramyadhadidi/summeries/blob/master/High_Performance_Computer_Architecture/coh1.png)
![Coh2](https://github.com/ramyadhadidi/summeries/blob/master/High_Performance_Computer_Architecture/coh2.png)
![Coh3](https://github.com/ramyadhadidi/summeries/blob/master/High_Performance_Computer_Architecture/coh3.png)



Conflict RAMYAD****
# N7: Synchronization and Consistency

## Synchronization
When using shared memory need to coordinate. Two types of synchronization:
- Barriers (Wait for all threads to reach to a place)
- Mutex (Make sure only one thread have access to a critical region)
- Alternative Solution: _Transactions_

### Mutex Implementations
- Test and Set (a spin lock, lots of getM)
- Test and Test and Set
- Tree locks (lock has levels so competition to get the lock is lesser)
- Queuing locks (Threads add themselves to a queue, finished thread wakes up the following)
- Reader writer locks (lock for readers are free, but writers should wait)

### Barrier Implementations
- Simple counter
- Tree barriers (counter becomes bottleneck in large-scale systems, use trees)
- 1-hot (single bit for each processor)
- more hardware supported (Load Linked (LL) and Store Conditional (SC))

## Consistency
- Coherence: Ordering parallel accesses to _same addresses_.
- Consistency: Ordering parallel accesses to _different addresses_.

### Strict Consistency
- The accesses by each processor were kept in-order (no R/W reordering)
- The accesses between different processors are ordered by _global clock_.
- The order is seen by everyone

### Sequential Consistency
- The accesses by each processor were kept in-order (no R/W reordering)
- The accesses between different processors were arbitrarily interleaved.
- The same order is seen by everyone

### Relaxed Consistency Models
Feature the bug and make the programmer's problem! Without data races they work just fine. But
with data races, the problem shows up.

#### Weak Consistency
- All syncs are sequentially consistent
- All write should be completed before a sync access
- No data access are allowed while we a sync access
Bad: all W/R before a sync should be completed, but we just care for critical sections variables

#### Release Consistency
- Acquire and releases are sequentially consistent
- Before a non-sync read, all acquires should be done
- Before a release, all R/W by the mutex-owning processor should be done

# N8: Interconnects
- Flits: Flow-Control Digits
- one packet = multiple flits

Topologies:
- Direct vs. Indirect
- Shared vs. Point to Point

## Torus/Mesh
K-array n-Torus:
- n dimensions
- Total nodes = K^n
- __Ring__ is when n=1

Torus vs Mesh:
- Each dimension of a torus has K nodes connected in a ring
- Each dimension of a mesh has K nodes connected in a line (first and last nodes not connected)

Rest is basic network concepts: routing, flow control, virtual channels
