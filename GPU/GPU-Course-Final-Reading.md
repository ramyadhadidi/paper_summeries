### [Warp Level Divergence: Characterization, Impact, and Mitigation](http://people.engr.ncsu.edu/hzhou/hpca14.pdf)
  - Problem: 
  With a TB-level resource management scheme, all the
  resource required by a TB is allocated/released when it is dispatched to / finished in a streaming
  multiprocessor (SM). TB-level resource management is not good. First, different warps in a TB may finish at different
  times, which we refer to as ‘warp-level divergence’. Due to TB-level resource management, the resources
  allocated to early finished warps are essentially wasted as they need to wait for the longest running
  warp in the same TB to finish. Second, TB-level management can lead to resource fragmentation.
  
  - Their Solution:
  we propose to allocate and release resources at the warp level. Warps are
  dispatched to an SM as long as it has sufficient resource for a warp rather than a TB.
  Furthermore, whenever a warp is completed, its resource is released and can accommodate a new warp. 

### [Dynamic Warp Formation: Efficient MIMD Control Flow on SIMD Graphics Hardware](https://www.ece.ubc.ca/~aamodt/papers/wwlfung.micro2007.pdf)
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

### [Thread Block Compaction for Efficient SIMT Control Flow](https://www.ece.ubc.ca/~aamodt/papers/wwlfung.hpca2011.pdf)

  - Problem: the benefits of DWF can be affected by the scheduling
    policy used to issue ready warps and memory
    systems that limit bandwidth to first level memory structures.
      - issuing ready warps with smaller size will fall behind from other: *starvation eddy*
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