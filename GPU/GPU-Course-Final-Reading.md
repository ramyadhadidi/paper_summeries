### Warp Level Divergence: Characterization, Impact, and Mitigation:
  - Problem: 
  With a TB-level resource management scheme, all the
  resource required by a TB is allocated/released when it is dispatched to / finished in a streaming
  multiprocessor (SM). TB-level resource management is not good. First, different warps in a TB may finish at different
  times, which we refer to as ‘warp-level divergence’. Due to TB-level resource management, the resources
  allocated to early finished warps are essentially wasted as they need to wait for the longest running
  warp in the same TB to finish. Second, TB-level management can lead to resource fragmentation.
  
  - Their Solution:
  we propose to allocate and release resources at the warp level. Warps are
  dispatched to an SM as long as it has sufficient resource for a warp rather than a TB. Furthermore, whenever a warp is completed, its resource is released
  and can accommodate a new warp. 

### Dynamic Warp Formation: Efficient MIMD Control Flow on SIMD Graphics Hardware
  - Problem: underutilization at warp level for threads. So, group threads sooner than PDOM
  - Solution DWF: Dynamically form warps rather than statically based on PDOM reconverge stack
    - should have more than one divergent warp for thread block
    - can merge to warps if:
      - same next PC
      - and have compliments active threads
    - this will change register file access, because each register should be accessible from all lanes.
      - traditionally we have multi-banked register files: When threads are grouped into warps "statically", each thread’s registers are at the same "offset" within the lane, thus requiring only a single decoder. For DWF we need to have separate decoder for each RF.