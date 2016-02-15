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
Tools for finding bottlenecks:
- vmu-tools
- Intel VTune

### Joo Hwan-Machine learning algorithms on PNM

### Eric Hein-Benchmarking Dynamic Graph Analytics
Dynamic graph that changes dynamically (network security) (graphBIG supports it)
- insert batches of 8M and processing it
- running analytics online 
- process and array list, and reduce holes in it (pointers or data)

### Minhaj-Multicore NDP: Thermal Management
- Concurrency is increasing in 3D stacking
- Timing of DRAM remains constant (t-RAS) but they use burst to use more external BW, but internal BW is slow
    - minimum transaction size that we can get is getting higher
    - Problem: emerging accelerator needs lower transaction size
    - Solution: Many narrow channels
      - have multiple channels and expliot parallelism
  - 2D network latency still dominates (you need to rout them in logic layer)
  - Load distribution to improve power and performance
- Thermal Wall in 3D memories
  - higher operating temperatures
  - lower heat removal capability 
- Proposal:
  - Different refresh rates for channels and banks at different temperatures
  - MicroFluids Cooling
  - per-bank refresh

### Xiao-Multicore NDP: Adaptive Power Management
- Pin stress problem: more processors to package, less pins for data, more power (needs more pin)
- Thermal coupling problem
- So, with this given limits of power budgets how to improve performance:
  - LLC access time with temp: 20C to 85C -> reduction 54%
  - IPC improves 13% in 20C than 85$ (splash2)
- Thermally adaptive architecture:

### Blaise-SecurePIM

### Duckwan Kim - NeuroCube
- requires massive storage, and simple MAC
- high destiny external memory accesses
- Putting on host: fast, less channels
- Putting on LL: slow, more channels (this)
- Compares GPU, FPGA and this
- Programmable Neurosesquence Generator (generate sequence access on HMC, with some counters and finite state machines)
- Thermal and area models in HMC designs

