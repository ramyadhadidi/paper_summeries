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
Extend variable scope, and reduce register usage. Brings saftley issues

## Control Flow Graph (CFG)
Basic blocks as nodes, and branches and fall-throughs as edges. We do not have the data, so and edge
means that program may take the path. 2 simple rules. Done in IR level.

### Form a EBB from CFG
All additional block should be only accessible from the root of EBB. We move through successors. 
