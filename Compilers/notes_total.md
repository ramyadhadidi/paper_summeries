<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Compiler Notes](#compiler-notes)
	- [IR Generation](#ir-generation)
	- [Basic blocks](#basic-blocks)
		- [Extended Basic Block](#extended-basic-block)
		- [DAG representation](#dag-representation)
	- [Dominators](#dominators)
		- [Dominator Tree](#dominator-tree)
	- [Loops](#loops)
		- [Strongly-connected component (SCC)](#strongly-connected-component-scc)
		- [Back-edges](#back-edges)
	- [Regions](#regions)
	- [Flow Analysis](#flow-analysis)
				- [Live Variable Analysis (Backward, union, ref->live def-> dead)](#live-variable-analysis-backward-union-ref-live-def-dead)
				- [Available Expression (Forward, intersection, gen->available kill->)](#available-expression-forward-intersection-gen-available-kill-)
				- [Very Busy Expressions (Backward, intersection)](#very-busy-expressions-backward-intersection)
				- [Postponable Expressions (Forward, intersection)](#postponable-expressions-forward-intersection)
				- [Reaching Definition (Forward, union)](#reaching-definition-forward-union)
				- [D-U and U-D chains (Use-Def, Def-Use)](#d-u-and-u-d-chains-use-def-def-use)
				- [Strength reduction](#strength-reduction)
				- [Induction Variables](#induction-variables)
	- [Partial-Redundancy Elimination (PRE)](#partial-redundancy-elimination-pre)
		- [Loop Invariant Statements](#loop-invariant-statements)
			- [Loop Invariant Code Motion](#loop-invariant-code-motion)
		- [Partial Subexpression Elimination](#partial-subexpression-elimination)
	- [Loop Optimizations](#loop-optimizations)
	- [Lazy Code Motion](#lazy-code-motion)
	- [SSA Form (Static Single-Assignment)](#ssa-form-static-single-assignment)
		- [Construction (Maximal SSA)](#construction-maximal-ssa)
		- [Construction (Minimal SSA)](#construction-minimal-ssa)
			- [Summery](#summery)
		- [Data Flow optimizations with SSA](#data-flow-optimizations-with-ssa)
			- [Dead Code Elimination](#dead-code-elimination)
	- [Value Numbering](#value-numbering)
		- [Local Value Numbering](#local-value-numbering)
		- [Global Value Numbering (GVN)](#global-value-numbering-gvn)
	- [Loop Restructuring](#loop-restructuring)
	- [Register Allocation](#register-allocation)
		- [Webs](#webs)
		- [Graph Coloring](#graph-coloring)
			- [Spilling](#spilling)
			- [Register Allocation Optimizations](#register-allocation-optimizations)
				- [Splitting](#splitting)
				- [Coalescing](#coalescing)
				- [Register Targeting](#register-targeting)
				- [Pre-Splitting](#pre-splitting)
				- [Interprocedural register allocation](#interprocedural-register-allocation)
	- [Dependency](#dependency)
		- [Data Dependency](#data-dependency)
			- [Classification](#classification)
			- [Bernstein's Conditions](#bernsteins-conditions)
			- [Loop Dependency](#loop-dependency)
				- [Terms and Theory](#terms-and-theory)
				- [Dependence Testing](#dependence-testing)
					- [Dependence Testing steps](#dependence-testing-steps)
					- [ZIV Test](#ziv-test)
					- [Strong SIV Test](#strong-siv-test)
					- [Weak-Zero SIV Test](#weak-zero-siv-test)
					- [GCD Test](#gcd-test)
					- [Banerjee Test](#banerjee-test)

<!-- /TOC -->



# Compiler Notes

## IR Generation
Types of IR:
	- Structural (Trees, DAG)
	- Linear (3 address code, stack machine code)
	- Hybrid (CFG)

Short Circuiting: while generating code for || or && if one of them is constant, ignore
based on operand type.

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
 - syntactic methods will overestimate liveness for safely
 - e.g. two if statements that one of them will be true and they define t.

![live](https://github.com/ramyadhadidi/summeries/blob/master/Compilers/live.jpg)

##### Available Expression (Forward, intersection, gen->available kill->)
- Redundant computation, CSE (common subexpression elimination)
- sem-avail(n) ⊇ syn-avail(n)
	- e.g. could always take one of the two if statements

![aexp](https://github.com/ramyadhadidi/summeries/blob/master/Compilers/aexp.jpg)

##### Very Busy Expressions (Backward, intersection)
- Lazy code motion, code hoisting

##### Postponable Expressions (Forward, intersection)
- Lazy code motion

##### Reaching Definition (Forward, union)
- Constant Folding, Constant Porpagation

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
 3. Postpone expressions
 4. Clean-up



<!-- **************************************************************************** -->
## SSA Form (Static Single-Assignment)
It is form of IR that every value is assigned just once. Therefore, it is possible to
have multiple values in SSA
pointing to a value in non-SSA format. So, we import &#934;(x,x,...,x) functions in every
joint point for all variables
in non-SSA form, the size for parameters are number of predecessors.

Simplifies and make more effective: Constant Propagation, Value numbering,
Invariant code motion, partial-redundancy elimination.

### Construction (Maximal SSA)
- Insert &#934; function at every joint for every name
- Solve reaching definition
- Rename each use to the def that reaches it

This will build __Maximal SSA__ which has too many unnecessary &#934; functions.

### Construction (Minimal SSA)
__Minimal SSA__ can be generated using Dominance Frontier.

__Dominance Frontier__: _DF(x)_ is the set of all nodes _y_ in the flow-graph x dominates an
immediate predecessor of _y_ but does not strictly dominates _y_.

Calculating DF(x) is quadratic in the number of nodes.
Here is the optimal algorithm for finiding DF(x):

![DFx](https://github.com/ramyadhadidi/summeries/blob/master/Compilers/DFx.jpg)

After this we might find where to insert &#934; functions partially. Because there
would exist some paths in future that joins. So We need __Iterated Dominator Frontier__
which is:

![DFx2](https://github.com/ramyadhadidi/summeries/blob/master/Compilers/DFx2.jpg)

So we find DF&#8734; for all basic blocks that a variable is defined, and then
insert the &#934; functions there.

After this we need to do variable renaming using a stacked-based algorithm. All lhs
should be subscripted, all rhs, use appropriate subscript.

After this, solve reaching definition to find what to put in &#934; functions.

#### Summery
In summery these are the steps:

1. Calculate Dominance Frontier for each variable, this is where we insert &#934; functions.
	- Find DF local
	- Find DF up
	- Find DF &#8734;
2. Rename variables
3. Solve reaching definition to populate &#934; functions inputs.

### Data Flow optimizations with SSA

#### Dead Code Elimination
- similar to mark and sweep
- start with critical operations (I/O, entry & exit, return values, calls)
- uses post-dominator to rewrite branches
- uses RDF to mark block-ending branch

<!-- **************************************************************************** -->
## Value Numbering
Give numbers to symbolic values, find equal expressions.
- take algebraic identities into account (x=x*1)
- take commutativity (a+b=b+a)

### Local Value Numbering
Data structure: Hash Table

How it works:
- implement a hash table
- for x+y, get their value numbers xv,xy
- look for (+,xv,xy)
- if there:
	- replace the computation by the reference
- if not:
	- if it has lhs (left-hand assignment)
		- assign the value number to it
	- else
		- create a temporary variable
		- assign the value number to it

How about redefined cases:
- z=x+y, z=w, v=x+y
- Options:
	1. every time create a temporary
	2. use SSA
	3. remove all hash table entries that uses lhs

It can be mixed to cover constant propagation/folding as well.

### Global Value Numbering (GVN)
Data structure: Value Graph

__Congruent__: Two variables are congruent iff their definitions have identical
operators and congruent operands.

Two values are equivalent iff at point P:
- they are congruent
- their defining assignments dominate P


<!-- **************************************************************************** -->
## Loop Restructuring
- Unswitching
	- remove loop independent conditionals from a loop
- Peeling
	- remove first (last) iteration of the loop
	- enables loop fusion
- Index Set Splitting
	- can remove conditionals from loop
	- enables loop fusion
- Scaler Exapnsion
	- eliminates anti and output dependence
	- loop must be countable
- Loop Fusion
	- Take two adjacent loops and create one
	- But should preserves dependence relations
	- Easy for data independent loops
	- For data dependent loops
		- should iterate same number of times
		- they are adjacent or can be
		- compiler must be able to use same induction variable
- Loop Fission
	- break a loop into two loops
	- it can improve cache
	- it can enable other transformation
- Loop Reversal
	- run a loop backward
	- legal for the loops that have no loop carried dependencies
- Loop Interchanging
	- reverse the nesting order
- Loop Skewing
	- Unnormalize iteration vectors to enable loop Interchanging
- Strip Mining
	- Decompose a loop in two nested loop for vector processors
- Loop Tiling

<!-- **************************************************************************** -->
## Register Allocation
SSA form or any IR uses many temporaries to hold variables. This make optimizations easier.
However, for code generation
we might have lesser registers than our temporaries number. Here, register
allocation problem arises.

To solve this, we use liveness analysis. The basic idea is that t1 and t2, if they are
live at the same time, they cannot
use same register. With this constraint, we build a register interference graph
(__RIG__). Vertices represent variables and
edges represent those variables that are live at the same time.

### Webs
Starting Point: def-use chains (DU chains)
- Connects definition to all reachable uses.

Conditions for putting defs and uses into same web
- Def and all reachable uses must be in same web
- All defs that reach same use must be in same web

Uses of webs make everything easier. First to du-chains by computing reaching definitions, then
compute maximal unions of intersection du-chanis. (each web is allocated to a register)

### Graph Coloring
To solve the problem, we can translate RIG to a graph coloring problem.
Where we find the minimum number of colors to color
the vertices so that no neighbor vertices are not the same color. (NP-hard)

Heuristic solution: consider we have k register.
- First Part:
  - pick a node t with less than k neighbor
	- if all nodes have equal or more than k neighbor
		- find a node with least spill cost (see [Spilling](#spilling))
		- push it to stack; continue
  - push it to stack
  - remove it with its neighbor from RIG
  - do until graph is empty
- Second Part:
  - pop stack
  - color it so no neighbors are with same color
  - pop until the stack is empty

#### Spilling
If we cannot use k registers to hold all values, we use memory.
When:
- All the nodes have k or more neighbor
  - If we remove the first node, all the nodes has k neighbors

We remove one node that has more than k neighbors, and save it to memory. Then try to allocate registers.

#### Register Allocation Optimizations

##### Splitting
When coloring fails, there are two options:
- spill (we talked)
- split the web that spills into multiple webs

How to choose:
- degree &#8805; k
- with minimal spilling cost
	- load and stores has more cost (10x) to power of loop depth
	- sum this for all defs and uses

So, split the web to reduce the degree. Then, at the point of split, spill to memory.

##### Coalescing
- Find register copy instructions sj = si
- If sj and si do not interfere, combine their webs

Pros:
- reduces number of instructions

Cons:
- may increase degree, so a colorable graph may become non-colorable

##### Register Targeting
 - Some variables need to be in special registers at a given time
 - So pre-color them first

##### Pre-Splitting
- Some live ranges have very large “dead” regions
- Find strategic locations to break-up (call sites, around loop to free registers)

##### Interprocedural register allocation
- Customize calling convention per function by doing interprocedural register allocation
- So less stack work and less memory load/store


<!-- **************************************************************************** -->
## Dependency
Two type of dependency:
- Data: our focus
- Control

### Data Dependency
There is data dependency from s1 to s2 if :
- both access the same memory location and at least one of them writes to it
- there is feasible run-time path from s1 to s2

It is heavily used for loop parallelizing. It also arises in many architectures, Vector processors,
SIMD and CMPs.

#### Classification
- True (RAW)
- Anti (WAR)
- Output (WAW)

#### Bernstein's Conditions
For dependency between iterations:
- i1 and i2 does not (read-write, write-read, write-write) to same location

#### Loop Dependency
Like data-dependency but between loop iterations: Two statements with two iteration vector
i and j such that i<j and they access same memory locations which at least one of them is a write.

We can reorder a loop as long as we preserves all dependencies.

Type of loop dependencies:

1. Loop-Independent: dependency exist but for iteration vector i=j
2. Loop-Carried: different iteration vectors. S1 depends on S2.
	- Forward: S2 appears after S1
	- Backward: S2 appears before S1 (or if S1=S2)

##### Terms and Theory

- _Iteration Vector_
- _Dependence Distance Vector_: From S1 to S2; d(i,j) = j - i
- _Dependence Direction Vector_: (<,=,>) :: source ? sink :: actually the sign of Distance Vector
	- <: legal
	- =: Loop-Independent dependence
	- >: illegal for single loop, legal of leftmost is a <
	- \*: shown it is possible to have multiple kind of dependencies
- _Level of a dependency_: subscript number of leftmost non-= component

Preserving all level-k dependence in Transforms:
 - Preserving iteration order of level k
 - does not interchange any loop with level<k inside the level-k loop
 - does not interchange any loop with level>k outside the level-k loop

Preserving Loop-Independent dependence:
 - A transform preserves the dependency if does not move instances between iterations, and relative order of statements.

##### Dependence Testing
Test whether dependence exist between two subscripted reference to same array or not.

Dependence testing assumes that we normalized iteration space, constant propagation ,
dead code elimination and induction variable substitution.

Two kind of test:
- conservative: if says there is not a dependency it is true, however, when it says there is a dependency it might not be a dependency.
- exact: find exactly if a dependency exists or not.

We can just test linear expressions for testing, no unknown or symbolic variables.

Type of indexes (number of subscripts in an index pair):
- ZIV (zero index variable)
- SIV (single index variable)
- MIV (multiple index variable)

if indexes do not occuer in other subscripts they are __separable__ else they are __coupled__.
ZIV and SIV pairs are usually separable, however MIV are not which makes them harder to test.

###### Dependence Testing steps

1. _Partition_ subscripts into separable and coupled groups.
2. Classify them as ZIV, SIV or MIV.
3. Perform appropriate test, if independence is proved exit.
4. Else, test for multiple subscripts tests, if independence proved exit.
5. Else, merge all dependence vector.

###### ZIV Test
If two subscripts are not equal, independence is proved. No further tests. Also, this test does
not provde direction vector (\*)

###### Strong SIV Test
coefficients of the index are equal: __<ai+c1, ai'+c2>__.

It has and answer (dependence exists) if __|d| &#8804; U - L__, Where __d = (c1 - c2) / a__.

###### Weak-Zero SIV Test
coefficients of the index with either a1=0 or a2=0: __<a1i+c1, a2i'+c2>__.

Then we can solve for i, and if __L &#8804; I &#8804; U__ the equation has an answer.

###### GCD Test
gcd of all coefficients should divide to b0-a0. If yes, there exists a solution in Real Numbers (R).
This is a conservative solution.

__(b0 - a0) / gcd(a1,a2,...,b1,b2,...)__ should have answer in Z.

###### Banerjee Test
f(a) - g(b) = 0 is the equation, find the minimum and maximum of it based on bounds. Then, if 0 is
not within the bound between minimum and maximum, then it has no solution.

or __minR h &#8804; 0 &#8804; maxR h__
