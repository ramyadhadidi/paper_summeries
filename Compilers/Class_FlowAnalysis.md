<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Flow Analysis](#flow-analysis)
  - [Some optimizations](#some-optimizations)
    - [Runtime overhead of function calls](#runtime-overhead-of-function-calls)
    - [Constant Branch Propagation](#constant-branch-propagation)
  - [Basic Block](#basic-block)
    - [Basic Block construction](#basic-block-construction)
    - [Extended Basic Block](#extended-basic-block)
  - [Control Flow Graph (CFG)](#control-flow-graph-cfg)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Flow Analysis
Constructs _Control Flow Graph_.

 - interprocedural - less beneficial, time consuming
 - intraprocedural 

Linear control flow: no branch and entry to code sequence

Cannot assign registers between linear control flow boundaries

## Some optimizations

### Runtime overhead of function calls 
**Function Inlining**

In -O0 function inlining is enabled. Save registers, branching, dereferencing, copying.

See what happens when disabling inlining

### Constant Branch Propagation
**Reduce Register File Pressure**

Use of Immediate operand which is faster. e.g. ADDI, ADD

Conditions:
  - linear code
  - no change to variables (no branch entering, exiting between)

## Basic Block
consecutive IR that only entering at beginning and leaving at end of it

Branch statement and branch target is easily distinguishable in Basic Block representation

### Basic Block construction
1. Leader
  - first statement of program
  - target of a branch (mostly labeled in languages)
  - any statement immediately after return or branch

### Extended Basic Block (EBB)
Extend variable scope, and reduce register usage. Brings safely issues

## Control Flow Graph (CFG)
Basic blocks as nodes, and branches and fall-through as edges. We do not have the data, so and edge
means that program may take the path. 2 simple rules. Done in IR level.

### Form a EBB from CFG
All additional blocks from BBB should be only accessible from the root of a single block in EBB. We move through successors.

### Dominator
A dominator block is a gate keeper for a block region. Every path must go through the dominator to reach to any node in region.
This happens mostly in loops.

A block could have multiple dominators. We call all dominators of a node _dominator set_ or _dom(node)_. Also, each node dominates itself.
This causes to capture self-loops.

Bunch of definition in slides. _proper dominators_, _immidiate dominator_.

If a node is in dominator set of node A, it should be also in dominator set of all predecessors nodes of node A.

Computing dominators is O(n^2) problem, Tarjan & Langeur algorithm made it O(n) (linear).

### Finding Loops
_back edge_: is an edge to the nodes that comes form its dominator nodes.

_natural Loops_:

### Points & Paths
Points: edge-related properties: after, before, and between statements of a BB

Path: a sequence of points


Unreachable code: dangling code (pure CFG)

-O0: inlining, unreachable code removal, constructing CFG

## Global Dataflow Analysis
Find _def_ and _use_ of variables. CFG is used to find if any def or use is reachable or not. These analysis is useful in transformations.
Some examples:
  - Constant propagation
  - Copy values
  - Branch balancing

_kill_ and _reach_ of a definition.

Q) Is using just _kill_ and _reach_ without dataflow analysis produce a safe code? If yes, so why we are doing dataflow analysis.

### Dataflow equation for each BB
  - _gen_ set: scan backward, for each definition insert it into gen set and stop looking for that def
  - _kill_ set: the ones the are redefined, and are not allowed to continue from that block
  - _in_ set: look for reach of that definition = U[P] : P isPartof pred[B]

### Working Set
### Loop merging

## Reaching Defintion Analysis

### D-U and U-D chains

### Note
Static over-optimization: do propagation for variables, therefore you will have more opportunity for optimizing. Do not do it for
dynamic variables, since it will be unsafe. Unsafe languages does not have this opportunity (C).

This brings another question that how we can derefrece pointers: 
 - In reaching definition analysis we do not need to worry about (L value?) (..=*p)
 - (p=&a, *p=...) this way is easy, we can easily derefrence and create reaching definition corretly
 - C pointers does not fit int these, we should not killi
 - Look at llvm pointer analysis
