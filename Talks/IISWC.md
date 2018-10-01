#Simulations
##Full Speed Ahead: Detailed Architectural Simulation at Near-Native Speed
    Using Gem5
    Their Simulator can offload instructions using KVM to host machine
        and they need to just simualtes I/O using Gem5
    Three phases: Virtual Fast Forwad, Fast (1MIPS), OoO (0.1MIPS)
        They are doing this 90%,5%,5% of time
    The do cache warming almost all of the time
        They also do some speculative slow simulation with hit/miss in cache

##Differential Fault Injection on Microarchitectural Simulators [**]
    Inject Faults at different levels in Gem5 and MARSSx86 and see the error type

#Systems
##SourceMark: A Source-Level Approach for Identifying Architecture and Optimization Agnostic Regions for Performance Analysis
    Uses Pin, SimPoint to identify where in source code we have performance bottleneck

##Characterizing Disk Failures with Quantified Disk Degradation Signatures: An Early Experience [*]
    50% of disk failurs are logical (no physical errors!`)

##PC Design, Use, and Purchase Relations [*]

#GPUSII
##3D Workload Subsetting for GPU architecture Pathfinding[*]

##Revealing Critical Loads and Hidden Data Locality in GPGPU Applications [*]

##Keynote
Commercial Big Data Workloads
Flavio Villanustre (HPCC Systems)
    Data Fabrication - Legalization - $1.7m/yr
    hpccsystems.lexisnexis.com
    Big Data -> Data Refinery (Thor) -> Data Delivery Engine (Roxie) -> ESP (service) -> Report
        Thor: Sorting, indexing (4to1 with Roxie)
        Roxie: Two components (Farmers: knows a node, Slave: knows segments)
    Data Centeric Languages (C++(500,000l) <- ECL(4,000l) <- SALT(40l))

#GPUSI
##Exploring Parallel Programming Models for Heterogeneous Computing Systems [Compiler/Languages]
    OpenACC (no sync threads, no shared memory)
    OpenMP4.0
    C++ AMP
    openCL

##GPU Computing Pipeline Inefficiencies and Optimization Opportunities in Heterogeneous CPU-GPU Processors [**]
    Joel Hestness (Wisc)
    Software pipeline inefficient in heterogeneous architecture
    For producer-cosumer Applications
        - finer grain concurrency neededs
        - lightweigh code for protability
        - reducing cache contention provides benefits
    k-mean algorithm:
        - select candidates (CPU)
        - distance calculation (GPU)
        - aggregate distance (CPU)
        - copy data
            ) baseline (most time not on GPU side) (copy includes in each round)
            ) Async copy (1.6x) (copying data)
            ) no copy (2.1x)
        Gem5-GPU [***]    https://gem5-gpu.cs.wisc.edu/wiki/
                                        http://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=6709764

##Fast Computational GPU Design with GT-Pin [Instrumentation]
    Dynamic binary instrumentation for Intel GPU
    Intel GPUs work with openCL

#Best Paper Nominees
##Performance Characterization for High-Level Programming Models for GPU Graph Analytics [Software-Level]

##Locality Exists in Graph Processing: Workload Characterization on an Ivy Bridge Server [*****]
    GAP Benchmark Suite
    http://gap.cs.berkeley.edu/

##CRONO: A Benchmark Suite for Multithreaded Graph Algorithms Executing on Futuristic Multicores [**]
    https://github.com/masabahmad/CRONO
    Footprint hundreds of MB
    LoneStar benchmark [***]


#Mobile Application
##Energy-Performance Trade-offs on Energy-Constrained Devices with Multi-Component DVFS [***]
    Inefficiency [***]
    Gem5 dvfs module [***]

##Characterization and Throttling-based Mitigation of Memory Interference for Heterogeneous Smartphones [**]
    Examining Performance Degredation for L2 Shared cache for mobie applications
    Again Core specific DVFS

##I/O Characteristics of Smartphone Applications and Their Implications for eMMC Design []

##Big or Little: A Study of Mobile Interactive Applications on an Asymmetric Multi-core Platform [*]
    Mobile Applications DVFS

#Keynote:
David Brooks:
    Technology Trends:
        Nodes->10nm 3years - 7nm 2020
        Technology: no replacement for CMOS till 2020
            ---> Golden Age of Design!
            Since now expetation from designers was low (1% speedup for 1% area::Intel Mantra)
            Architects need to aim for more than 10X speedup rather than 10% conventional speedups in academia
            Startup will show up since time is longer-novelty-new ideas
        Solutions:
            Going beyond Homogeneous parallelism:
                2D graph of (programmabilty, in-core and energy efficiency) [image]
                Benefits of specializaion towards energy [Brodersen 2002]
                Has been embrace even now(Apple A8 SoC: CPU,GPU,Out-of_core Acceletors 30x)
                Acceletors Architecture
                Challenges in Acceletors:
                    -Flexibility (How to decompose applications)
                    -Programmability (How to rapidly design them)
                    -Design Cost (How to manage shared resources)
                Reaserch:
                    Accelerator Memory Systems Design [CAL'10]
                    Specialized HW
                    Aladdin: A pre-RTL accelerator model [ISCA]
                        C code -> LVM IR -> Dynamic Data Dependence Graph (DDDG)
        Profiling High-Scale Warehouse servers [ISCA]:
            Google contunuous profiling (GWP)
            Sampling performance counters on google servers
            Idle cycles - no hotspot within applications
            Hotspot accross applications (30% of total cycles)
        Example for Bee like Insect [VLSI'15]

#Tutorial:
##Wind River® Simics and Intel® SAE: Dynamic Binary Instrumentation of OS Kernel, Driver and BIOS
    Application + kernel instruments
    Request for package with your professor
