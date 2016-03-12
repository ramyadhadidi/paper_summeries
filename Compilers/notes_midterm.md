
## Basic blocks
Three rules to find the leaders:
  - first statement
  - target of a branch
  - fall-through
  
There is an edge between basic blocks if (i) branch, (ii) immediately follows

**Points**: between statements

**Path**: a sequence of points

### Extended Basic Block
An extended basic block is a maximal collection of basic blocks where:

- only the first basic block can have multiple predecessor basic blocks;
- all the other basic blocks have one single predecessor basic block, which must be within the collection of basic blocks.

### DAG representation
Contracted AST that shows sharing explicitly. It is good for: 
  - local common subexpression
  - dead code elimination (with live variable analysis) 
  - strenght reduction 
  - reordering




## Dominators

Dominator set node a **Dom(a)**: all nodes that dominates a

- a **dominates** b :: a **&#8804;** b
- a **properly dominates** b :: a **<** b (a&#8804;b and a&#8800;b)
- a **directly dominates** b :: a **< d** b (there is no c such that a<c<b)

### Dominator Tree
In a dominator tree the start node s is the root, and each
node d dominates only its descendents in the tree.






## Loops

### Strongly-connected component (SCC)
SCC: A subgraoh that there is path from each node to every node.

How to find them: [here](https://www.youtube.com/watch?v=ju9Yk7OOEb8)

A strongly-connected component G’ = (N’, E’, s’) of a flowgraph G = (N, E, s) is a 
**loop** with entry s’ if s’ dominates all nodes in N’.

### Back-edges

An edge (b,a) of a flowgraph G is a back edge if a dominates b, a < b.

**Natural Loops**: Given a back edge (b,a), a natural loop associated with (b,a) 
with entry in node a is the subgraph formed by a plus all nodes that can reach b without going through a.

How to:
 - find a backedge (b,a)
 - find all nodes dominated by a
 - look for nodes that can reach b among nodes dominated by a



## Regions
A region is a set of nodes N that include a header with the following properties:
 - the header must dominate all the nodes in the region;
 - All the edges between nodes in N are in the region (except for some edges that enter the header);

A loop is a special region that has the following additional properties:
 - it is strongly connected;
 - All back edges to the header are included in the loop;





## Flow Analysis
 - Control Flow Analysis
 - Data Flow Analysis
 
__Code optimization__: a program transformation that preserves correctness and improves performance.
it may be perfromed on different levels: source code, IR, target machine code

##### Live Variable Analysis (Backward, union, ref->live def-> dead)
- Dead code elimination, uninitialized variables, register and memory allocation
- sem-live(n) ⊆ syn-live(n)
- in-live(n) =  out-live(n) \ def (n)  ∪ ref (n)

##### Available Expression (Forward, intersection, gen->available kill->) 
- Redundant computation
- sem-avail(n) ⊇ syn-avail(n)
- out-avail(n) =  in-avail(n) ∪ gen(n)  \ kill(n)

##### Reaching Definition (Forward, union)

##### D-U and U-D chains (Use-Def, Def-Use)
No needed when using SSA form

##### Strenght reduction
Replace expensive operations with cheaper ones.

##### Induction Variables
A induction variable is a variable if there is constant c such that each time x is assigned its value increases by c.
```
j=j-1
t4 = j*4  --> t4 = t4-4
```




## SSA Form (Static Single-Assignment)
It is form of IR that every value is assigned just once. Therefore, it is possible to have multiple values in SSA 
pointing to a value in non-SSA format. So, we import &#934;(x,x,...,x) functions in every joint point for all variables 
in non-SSA form, the size for parameters are number of predessesors.

Simpilifes and make more effective: Constant Propagation, Value numbering, Invariant code motion, partial-redundancy elimination
