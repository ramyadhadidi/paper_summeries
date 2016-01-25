# Flow Analysis
Constructs _Control Flow Graph_.

 - interprocedural - less benefitial, time consuming
 - intraprocedural 

linear control flow: no branch and entry to code sequence

cannot assign registers between linear control flow boundries

## Some optimizations

### Runtime overhead of function calls 
**Funtion Inlining**

in -O0 function inlining is enabled. save registers, branching, dereferencing, copying.

See what happens when disabling inlining

### Constant Branch Propagation
**Reduce Register File Pressure**

use of Immediate operand which is faster. e.g. ADDI, ADD

Conditions:
  - linear code
  - no change to variables (no branch entring, exiting between)

## Basic Block

