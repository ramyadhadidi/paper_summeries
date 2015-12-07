### Warp Level Divergence: Characterization, Impact, and Mitigation: Loc(6.4 (RF File))
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


