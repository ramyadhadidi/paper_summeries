## Flow Analysis
 - Control Flow Analysis
 - Data Flow Analysis
 
__Code optimization__: a program transformation that preserves correctness and improves performance.
it may be perfromed on different levels: source code, IR, target machine code

### Basic blocks
Three rules to find the leaders:
  - first statement
  - target of a branch
  - fall-through
  
There is an edge if (i) branch, (ii) immediately follows

#### Extended Basic Block
An extended basic block is a maximal collection of basic blocks where:

- only the first basic block can have multiple predecessor basic blocks;
- all the other basic blocks have one single predecessor basic block, which must be within the collection of basic blocks.


### Dominators

Dominator set node a **Dom(a)**: all nodes that dominates a

- a **dominates** b :: a **&#8804;** b
- a **properly dominates** b :: a **<** b (a&#8804;b and a&#8800;b)
- a **directly dominates** b :: a **< d** b (there is no c such that a<c<b)

### Strongly-connected component (SCC)
SCC: A subgraoh that there is path from each node to every node.

How to find them: [here](https://www.youtube.com/watch?v=ju9Yk7OOEb8)

A strongly-connected component G’ = (N’, E’, s’) of a flowgraph G = (N, E, s) is a 
loop with entry s’ if s’ dominates all nodes in N’.
