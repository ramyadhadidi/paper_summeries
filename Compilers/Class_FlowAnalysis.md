<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [Flow Analysis](#flow-analysis)
  - [Some optimizations](#some-optimizations)
    - [Runtime overhead of function calls](#runtime-overhead-of-function-calls)
    - [Constant Branch Propagation](#constant-branch-propagation)
  - [Basic Block](#basic-block)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# Flow Analysis
Constructs _Control Flow Graph_.

 - interprocedural - less beneficial, time consuming
 - intraprocedural 

linear control flow: no branch and entry to code sequence

cannot assign registers between linear control flow boundaries

## Some optimizations

### Runtime overhead of function calls 
**Function Inlining**

in -O0 function inlining is enabled. save registers, branching, dereferencing, copying.

See what happens when disabling inlining

### Constant Branch Propagation
**Reduce Register File Pressure**

use of Immediate operand which is faster. e.g. ADDI, ADD

Conditions:
  - linear code
  - no change to variables (no branch entering, exiting between)

## Basic Block
consecutive IR that only entering at beginning and leaving at end of it

branch statement and branch target is easily distinguishable in Basic Block representation

### Basic Block construction
1. Leader
  - first statement of program
  - target of a branch (mostly labeled in languages)
  - any statement immediately after return or branch
