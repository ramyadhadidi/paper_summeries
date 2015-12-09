### [Warp Level Divergence: Characterization, Impact, and Mitigation](http://people.engr.ncsu.edu/hzhou/hpca14.pdf)
  - Problem: With a TB-level resource management scheme, all the
  resource required by a TB is allocated/released when it is dispatched to / finished in a streaming
  multiprocessor (SM). TB-level resource management is not good. First, different warps in a TB may finish at different
  times, which we refer to as 'warp-level divergence'. Due to TB-level resource management, the resources
  allocated to early finished warps are essentially wasted as they need to wait for the longest running
  warp in the same TB to finish. Second, TB-level management can lead to resource fragmentation.
  
  - Their Solution:
  we propose to allocate and release resources at the warp level. Warps are
  dispatched to an SM as long as it has sufficient resource for a warp rather than a TB.
  Furthermore, whenever a warp is completed, its resource is released and can accommodate a new warp. 

### [Dynamic Warp Formation: Efficient MIMD Control Flow on SIMD Graphics Hardware](https://www.ece.ubc.ca/~aamodt/papers/wwlfung.micro2007.pdf) **DWF**
  - Problem: underutilization at warp level for threads. So, group threads sooner than PDOM
  - Solution DWF: Dynamically form warps rather than statically based on PDOM reconverge stack
    - should have more than one divergent warp for thread block
    - can merge to warps if:
      - same next PC
      - and have compliments active threads
    - this will change register file access, because each register should be accessible from all lanes.
      - traditionally we have multi-banked register files: When threads are grouped into warps "statically", 
      each thread’s registers are at the same "offset" within the lane, thus requiring only a single decoder. 
      For DWF we need to have separate decoder for each RF.

### [Thread Block Compaction for Efficient SIMT Control Flow](https://www.ece.ubc.ca/~aamodt/papers/wwlfung.hpca2011.pdf) **TBC**
  - Problem: the benefits of DWF can be affected by the scheduling
    policy used to issue ready warps and memory
    systems that limit bandwidth to first level memory structures.
      - issuing ready warps with smaller size will fall behind from others: *starvation eddy*
      - threads in a warp usually access nearby memory addresses so more coalescing. But, DWF tries to optimize 
      control flow by merging different warps, therefore increasing memory references.
  - In general, we observed three problems when running CUDA applications on DWF enabled execution model: 
    1. Applications relying on implicit synchronization in a static warp (e.g. NVRT) execute incorrectly
    2. Starvation eddies may reduce SIMD efficiency
    3. Thread regrouping in DWF increases non-coalesced memory accesses and shared memory bank conflicts
  - Insight: Threads inside a block provide sufficient "control-flow locality", so do a simple DWF just within a TB with a TB-wide reconverge stack.
  - Solution:
    - Extension to DWF: Warp barrier: keeps threads in their original static warp after reconverge. When reach a divergent
    point, insert barrier before immediate post-dominator. (This will solve fig1.a problem)
  - Solution: TBC
    - block-wide reconverge stack: at divergence, the warps *synchronize* and compacted into new warps.
    - In reconvergence point, threads will again *synchronize* and restore their static warp formation
      - The synchronization overhead at branches can be covered by switching the execution to a different thread block running on the same SIMT core
  - **Likely convergence point**: they extend the PDOM reconvergence stack to capture rarely taken divergence and reconverge earlier.
    - the likely convergence point is identified with profiling or control flow analysis (cons)
    - insert two more column to stack: LPC(Likely convergence point), LPos(stack position of likely convergence point)
    - on divergence insert Likely reconverge point as well with zero active threads
      - for each stack access check if PC=LPC, then insert that stack active thread mask to LPos entry
    - study (Fig10)
    - benefit: smaller stack size + small speedup

### [Maximizing SIMD Resource Utilization in GPGPUs with SIMD Lane Permutation] (https://dl.acm.org/citation.cfm?id=2485953) **SLP**
  - current GPUs statically assign a fixed SIMD lane to each thread based on its thread-ID in a sequential manner.
  - *aligned divergence* originates from a branch whose condition is dependent only on programmatic values (e.g., CTA-/warp-/thread-ID, scalar input
    parameters), in which case the divergence pattern exhibits similarly across warps.
  - Problem of compaction:  
      1. The alignment of a thread in a SIMD group is fixed, limiting the potential for compaction. Control frequently diverges in a manner that prevents compaction because of the way in which the fixed alignment of threads to lanes is done.
      2. synchronization overhead at divergence and reconverge
  - Solution: SLP
    - permute home lane of threads when making a new Warp
    - doing this at first, by changing lane mapping

### [SIMD Divergence Optimization Through Intra‐Warp Compaction](https://dl.acm.org/citation.cfm?id=2485954)
  - **Note**: In Intel’s Ivy Bridge GPUs, the number of lanes in a SIMD instruction (sometimes referred
    to as the warp-width) varies from 8, 16 to 32. This number is 32 for NVIDIA GPUs and 64 for AMD’s GPUs. However, the corresponding number of hardware
    execution lanes is typically a fraction of the SIMD instruction width: 4-wide SIMD ALU in the case of Intel’s Ivy
    Bridge GPU, 8-wide for NVIDIA, and 16-wide for AMD Radeon. This implies that each wide SIMD instruction typically executes over multiple execution cycles due to narrower hardware width. Why?
      - register file concurrent read/write limits
      - dependency not needed
      - pipeline data forwarding not needed
  - Intel Ivy Bridge GPU Architecture:
    - shader cores: execution units (EU) organized in subslices of 8 EUs 
    - each EU have 6-8 hardware threads
    - each EU thread is a SIMD core 
      - Each thread executes a kernel
      - Numbers: 24 EU, each EU 6 threads -> 144 threads
    - instructions can be variable in SIMD lane requirements (Intel CISC)
    - each thread has its own explicit PC in contrast with AMD and NVidia GPUs
    - Mapping to BSP Model -> all threads in TB to a single EU thread
  - **Paper**: simple execution cycle compression techniques when certain groups of turned-off lanes exist in the instruction stream
    - BCC: basic cycle compression: where contiguous sets of channels(lane) are disabled
    - SCC: swizzled-cycle compression: where turned off channels are not contiguous. Group them with swizzling(permutation) to enable dead cycles harvest. (Permutation first with crossbar, then compaction)

### [The Dual Path Execution Model for Efficient GPU Control Flow](https://lph.ece.utexas.edu/merez/uploads/MattanErez/hpca2013_dpe.pdf) **DPE**
  - Opportunity: Reconvergence-based techniques permit only a single divergent path to be active at any point in time
    - Execute two path by interleaving them
  - **Note**: NVidia SM from a warp of 32 threads *and* AMD SIMD core form wavefront of 64 workitems
  - There are two main reasons why SIMD utilization decreases with divergence:
    1. masked operations needlessly consume resources (Solved by DWF,TBC,SLP)
    2. execution of concurrent control paths is serialized with every divergence (This paper)
  - previous research: dynamic warp subdivision (**DWS**) (fig3)
    - The basic idea of DW is to treat both the left and right paths of a divergent branch as independently schedulable units, or warp-splits, such that
      diverging path serialization is avoided and intra-warp latency tolerance is achieved
    - uses traditional reconverge stack or warp-split table (WST) based on a threshold of instruction number
    - problem: 
      1. uses RPC of last stack entry, rather than current divergent path. This continues for further division as well. Therefore, further subdivision cannot reconverge.
      2. DWS attempts to dynamically recombine warp-splits, but is not guaranteed.
      3. a warp is subdivided only if the divergent branch’s immediate post-dominator is followed up by a short basic block of no more than N instructions
  - **DPE**:
    - stack entries now track pathR and pathL together in a single row. RPC is same, active mask and PC is different.
    - when one of the PCs matches RPC, that path is invalidated. If both path is invalid, then entry is popped
    - dependencies: Recent GPUs from NVIDIA, such as Fermi, allow threads within the same warp to be issued back to back using a per-warp scoreboard to track data dependencies
      - to support DPE: two scoreboards for two path + shadow bit for each entry
      - on divergence pending bits are copied to shadow bit. On query we just check shadow bits of two scoreboards. on write-back they are reseted. Therefore, can concurrently execute r4 E-D. or can wait on r0 A-C.
      - section 3.2 + fig6
    - *non-interleavable branch*: the branch with just if and not any else statement
    - Metric for potential: Avg_Path = Sigma(i=1,N) NumPath_i / N    N::total number of warp instructions      NumPath::total number of concurrently schedulable paths

### [Improving GPU Performance via Large Warps and Two-Level Warp Scheduling](https://users.ece.cmu.edu/~omutlu/pub/large-gpu-warps_micro11.pdf) **LWM**
  - **Note**: GPUs utilize the *thread-level parallelism*, GPUs exploit TLP in two major ways:
    1. threads executing the same code are grouped into fixed sized batches known as warps (scaler frontend and SIMD backend)
    2. GPUs concurrently execute many warps on a single core
  - **Note**: *barrel processing*: A barrel processor is a CPU that switches between threads of execution on every cycle. 
    This CPU design technique is also known as "interleaved" or "fine-grained" temporal multithreading.
  - Problem: 
    1. divergence (complex control flow) & unable to hide memory latency between warps
    2. not able to hide memory latency
      1. Round-Robing all warps progress same rate and same request times
      2. Greedy, destroy data locality and starvation
  - Goal:
    1. Large Warp Microarchitecture (LWM): create large warp and then dynamically create sub-warps, this way even in divergence we will have large number of active threads
    2. two level Round-Robin: exploit data locality between warps while hiding latency
  - Current Warp Scheduling inside a SM: In the fetch stage, the scheduler selects a warp from the list of ready warps using a round-robin policy that gives equal priority to each warp.
  - LWM: same as DWF but form warps by looking inside large-warp
    - barrel processing consideration: Once a large warp is selected, it is not reconsidered for scheduling until the first sub-warp completes execution
    - re-fetch policy: Must wait till last sub-warp finishes
    - last warp effect in divergence decision (must wait until all sub-warps are done) and effect of last thread as well
    - sub-warps compaction optimization (same as DWF)
    - dependency check as well: Thread not available for packing into a sub-warp unless previous issue sub-warp has completed - a single bit status for each thread to see if it is packed or not
    - same changes as DWF to reg-file: separate decoder per RF bank
  - Two level Round-Robin: divide warps in fetch groups and give priority to one of them. Continue fetching from that groups warps in a round-robing fashion until reach stall. Then, change
    the group.
    - for mixing it with LWM need to add a timeout. Since, a large warp might starve other warps more likely.

### [Cache-Conscious Wavefront Scheduling](https://www.ece.ubc.ca/~aamodt/papers/tgrogers.micro2012.pdf) **CCWS**
  - **Note**: Belady-optimal replacement policy: evict the lines from the cache the was re-referenced furtherest
  - *Note*: thread::workitem wavefront::warp workgroup::threadBlock
  - Observation: Majority of data reuse by a warp comes from intra-warp locality
    - Adding more wavefronts to hide latency traded off against creating more long latency memory references due to thrashing
  - Goal: Schedule warps somehow that scheduler does not destroy intra-warp locality
    - How: with *lost intra-wavefront locality detector* which will keep a locality score for each warp and ensures that 
      scheduler will not destroy it.
  - *Static Wavefront Limiting* (SWL): an API that programmer based on his program workset size defines how many
    wavefront are allowed to be executed concurrently.
  - CCWS: change SWL to dynamic
    - Keep track of locality scores. Warp with largest score drop to bottom of the list(fig6), and warps with
      lowest score are close to cutoff. In fact, this policy decreases the re-reference period for warps with highest
      score.
    - LLD: Lost locality detector 
      - have VTA (victim tag array) for each wavefront. 
      - keep a score of LLS (locality lost score). Start from a baseline
      - for a wavefront if hit in VTA, increase score, else decrease score by one until baseline
      - cumulative LLS cutoff = NumActiveWaves * BaseLocalityScore

### [OWL: Cooperative Thread Array Aware Scheduling Techniques for Improving GPGPU Performance](https://users.ece.cmu.edu/~omutlu/pub/owl_asplos13.pdf) **OWL**
  - observation: they identify that the scheduling decisions made by warp scheduling policies are agnostic to thread-block behavior. 
    - Long memory stalls due to RR scheduling 
  - c(O)operative thread array a(W)are warp schedu(L)ing policy: OWL policy is a four-pronged concerted
    1. *CTA-aware two-level warp scheduler*: make smaller group of CTAs, RR
    2. *locality aware warp scheduler*: option 1 with prioritize one group until done
    3. *bank-level parallelism aware warp scheduling*: significant DRAM page locality between consecutive CTAs. Hence, less bank level parallelism (BLP). option 2 with this change. *between cores*
    4. *opportunistic prefetching*: recover the loss in DRAM row locality from option 3. Implement opportunistic prefetching mechanism, bring the data from the opened row to the nearest on-chip L2 cache
  - CTA-aware two-level warp scheduler (CTA-Aware):
    - group CTAs in smaller groups and prioritizes one group over others until all warps inside that groups is block-wide. After that next group
    - each group should have a minimum number of warps based on pipeline size and ...
  - Locality aware warp scheduler (CTA-Aware-Locality):
    - Prioritize one group to reduce L1 misses
    - same as above, but as soon as prioritized group is ready will switch to it
  - bank-level parallelism aware warp scheduling (CTA-Aware-Locality-BLP):
   - improves BLP
   - consider two cores with CTA-Aware-Locality scheme. If CTAs are consecutive, then small bank number will be utilized
   - ensure that non-consecutive CTAs are always prioritized in different cores
  - opportunistic prefetching: 
    - we lose DRAM Row locality by BLP
    - prefetch already open lines to L2

### [CudaDMA: optimizing GPU memory bandwidth via warp specialization](https://ppl.stanford.edu/papers/sc11-bauer.pdf)
  - decoupling data-movement, computation and synchronization in CUDA
  - CudaDMA, an extensible API for efficiently managing data transfers between the on-chip and off-chip memories of GPUs
  - CudaDMA enables the programmer to decouple the size and dimensionality of the data from the size and dimensionality of the computation, improving both programmability and performance
  - Decoupling is achieved by specializing warps into compute warps and DMA warps
  - There three factors related to data transfer that limit compute capability:
    1. Many long-latency memory accesses
    2. Coarse-grained synchronization
    3. Data Access Patterns
  - Achieving high MLP on a GPU can commonly be impeded by two factors:
    1. Instruction Issue
    2. Data Access Patterns
