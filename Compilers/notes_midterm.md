
<!-- **************************************************************************** -->
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



<!-- **************************************************************************** -->
## Dominators

Dominator set node a **Dom(a)**: all nodes that dominates a

- a **dominates** b :: a **&#8804;** b
- a **properly dominates** b :: a **<** b (a&#8804;b and a&#8800;b)
- a **directly dominates** b :: a **< d** b (there is no c such that a<c<b)

### Dominator Tree
In a dominator tree the start node s is the root, and each
node d dominates only its descendants in the tree.



<!-- **************************************************************************** -->
## Loops

### Strongly-connected component (SCC)
SCC: A subgraph that there is path from each node to every node.

How to find them: [here](https://www.youtube.com/watch?v=ju9Yk7OOEb8)

A strongly-connected component G’ = (N’, E’, s’) of a flow-graph G = (N, E, s) is a 
**loop** with entry s’ if s’ dominates all nodes in N’.

### Back-edges

An edge (b,a) of a flow-graph G is a back edge if a dominates b, a < b.

**Natural Loops**: Given a back edge (b,a), a natural loop associated with (b,a) 
with entry in node a is the subgraph formed by a plus all nodes that can reach b without going through a.

How to:
 - find a back-edge (b,a)
 - find all nodes dominated by a
 - look for nodes that can reach b among nodes dominated by a


<!-- **************************************************************************** -->
## Regions
A region is a set of nodes N that include a header with the following properties:
 - the header must dominate all the nodes in the region;
 - All the edges between nodes in N are in the region (except for some edges that enter the header);

A loop is a special region that has the following additional properties:
 - it is strongly connected;
 - All back edges to the header are included in the loop;




<!-- **************************************************************************** -->
## Flow Analysis
 - Control Flow Analysis
 - Data Flow Analysis
 
__Code optimization__: a program transformation that preserves correctness and improves performance.
it may be performed on different levels: source code, IR, target machine code

##### Live Variable Analysis (Backward, union, ref->live def-> dead)
- Dead code elimination, uninitialized variables, register and memory allocation
- sem-live(n) ⊆ syn-live(n)
- in-live(n) =  out-live(n) \ def (n)  ∪ ref (n)

##### Available Expression (Forward, intersection, gen->available kill->) 
- Redundant computation, CSE (common subexpression elimination)
- sem-avail(n) ⊇ syn-avail(n)
- out-avail(n) =  in-avail(n) ∪ gen(n)  \ kill(n)

##### Very Busy Expressions (Backward, intersection)
- Lazy code motion, code hoisting

##### Postponable Expressions (Forward, intersection)
- Lazy code motion

##### Reaching Definition (Forward, union)

##### D-U and U-D chains (Use-Def, Def-Use)
No needed when using SSA form

##### Strength reduction
Replace expensive operations with cheaper ones.

##### Induction Variables
A induction variable is a variable if there is constant c such that each time x is assigned its value increases by c.
```
j=j-1
t4 = j*4  --> t4 = t4-4
```



<!-- **************************************************************************** -->
## Partial-Redundancy Elimination (PRE)
Minimizing the number of expression evaluation that is called. 
 - Subexpression(common & partial)
 - loop-invariant expression

_Partial_ means that this redundancy is found in some paths and not all of them.

### Loop Invariant Statements
A statement is loop invariant if operands are:
- Constant,
- Have all reaching definitions outside loop, or
- Have exactly one reaching definition, and that definition comes from an invariant statement

#### Loop Invariant Code Motion
If an expression is not redefined inside a loop we can move it outside the loop to reduce the number of re-computations.
However, we should take care of cases when loop is not executed. Then:
 - the moved instruction will get executed and might throw and exception
 - if loop exits early, the optimized program may take longer

Therefore, to solve this, compilers add another if condition before the while (or for).

### Partial Subexpression Elimination
It can be changed to common subexpression elimination by copying the evaluation to the not evaluation path.

For more complex cases:

**Critical Edge**: any edge from a node with two successors to a node with more than one predecessor. 

We can create some new blocks along critical edges to do elimination for some expressions.

## Loop Optimizations
 - Loop-invariant code motion
 - strength reduction
 - induction variable elimination
    - derived from base
    - derived from variables


## Lazy Code Motion
Move a computation to earliest point to cover more redundant expressions. Then, compute the value as late as possible,
therefore reducing register pressure. This can happen in PRE. Anticipation limits how early we can put an expression.

_postponable expression_: e is postponable to a program point p if an early placement of e is ecountered along every path
from the entry node to p, and there is not subsequent use of e after the last such placement.

 1. Find all very busy expressions.
 2. Place computation at very busy points. (earliest points are where expressions in anticipated but not available)
    - Note: Avalabilty is little different in this case. It inclueds anticipation as well, e.g. it could be mad available if
      we chose to compute it there.
    - earliest[B] = anticipated[B].in - available[B].in
 3. ?



<!-- **************************************************************************** -->
## SSA Form (Static Single-Assignment)
It is form of IR that every value is assigned just once. Therefore, it is possible to have multiple values in SSA 
pointing to a value in non-SSA format. So, we import &#934;(x,x,...,x) functions in every joint point for all variables 
in non-SSA form, the size for parameters are number of predecessors.

Simplifies and make more effective: Constant Propagation, Value numbering, Invariant code motion, partial-redundancy elimination
