## PIM Retreat
## Feb 12

### Suda

perf(ops/s) = power(W) * efficiency(ops/J)

- Data movement energy has negative impact on efficiency
- Balance between energy and performance (energy per op, energy per byte)
- Pin numbers increasing to support current, less data pins
    - Data pin BW is not growing as fast as transistors
- Data Tsunami
- Wide IO, HBM, HMC
- Where $$ goes in systems

### Hyesoon: XPS, FULL, Cymric: NDP
Challenges:
  - Programming Model (data sharing, computation)
  - Design of PNM (SPFU, general purpose, mobile cores)
  - Thermal Problems
  - Memory Systems Problems:
    - coherency
    - consistency
    - page mapping/allocation schemes

Programming Models:
(more core centric to down)
  - Helper Computation 
    - (non-invasive: prefetching, invasive: SIMD compaction, data reorganization, data encryption)
    - Only accelerates memory operations
  - Offloading
    - instruction and task offloading
    - GPU as an example, atomic, gather scatter, traversing, convolution
    - What to offload and what to not offload?
  - peer computation
    - both are equal citizens of computation
    - challenges: cache coherency/consistency 
    - e.g., bounded staleness synchronization
  - owner computation
    - computation is triggered where the data is owned
    - data triggered architectures (Tullsen13)

Special hardware vs. Neumann Machine
  - Commands  vs. LD/ST 

### Meghana-HARP Compiler

### Chad-CHDL

### Ramyad-SST-MacSim-VaultSim
Questions:
-  Memory controller
- voltage scaling
- configurability in code level 

### Jeff-Sparse Primitive for Stacked Memories
Find primitive algorithms that can benefit using stacking memory. (scatter, gather). Actual hardware emulation using OpenHMC.
- FlatHMC(on board DRAM) + OpenHMC
- They use openCL

### Lifeng-PIM offlading for graph computing

