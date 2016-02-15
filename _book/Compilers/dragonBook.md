
<!-- **************************************************************************** -->
# Chapter 1 - Introduction

Interpreters: Directly execute the source 

Compilers: translate source to target language

Java example: source code -> bytecodes which then executes on a virtual machine

## Structure of a Compiler

Phases:
![](https://2.bp.blogspot.com/-SLv7cpLn7JU/T-t2rUlim9I/AAAAAAAAAYE/fvr2cM1sNmw/s1600/compiler.PNG)

front-end and back-end / symbol table / analysis and synthesis

### Lexical Analysis 

Creates <_token name_, _attributes_>, and pass it as a token to syntax analysis. It saves attributes into _symbol table_ and passes the pointer with token name.

### Syntax Analysis

Get the first value tokens (id, sum, assignment,...) and creates the syntax tree based on context-free grammars.

### Semantic Analysis

Check to see if has semantic consistency. Also checks for data types and imports it into tables. Also, does _coercions_ which is type conversion enforced by semantics.

### Intermediate Code Generation

Used to easily create target language. Should be simple and also easy to convert to machine language. One form is __three-address code__.

### Code Optimization

Is done on IR and is independent from machine language. In addition, there could be another code optimization on machine language.

### Code Generation

Generates the machine language code. Also, allocated registers and memory locations.

## The Evolution of Programming Languages

Different classification of languages. _First generation languages_ are the machine languages, _second generation_ is assembly, _third generation_ is higher-level languages like Fortran, Java, C and C++, _forth generation_ is the one designed for specific applications like SQL and _fifth generation_ is logic- and constraint-based like Prolog and OPS5.

Another view is _imperative_ and _declarative_. In imperative languages like C++, programmer specifies how a computation must be done. There is notion of program state and statements that change the state in imperative languages. Functional languages like Haskell and Prolog are declarative.

_Scripting languages_ are languages with high-level operators designed for gluing together computation. Like Python, Ruby, Perl.

<!-- ******************************************************************************** -->
# Chapter 7 - RunTime Environment

## Storage
 - _padding_, _aligned_, _stack_, _heap_.
 - Activation Tree: how functions are executed
 - Head needs _grabage collection_. Provided by some languages
 - Activation Records, _callee_ and _caller_ responsibilities
    - Callee is code is inserted once
    - Caller code multiple time
    - So it is better to save the records all within callee, but some information is not accessible by callee (top-stack, pc)
    - usually caller just allocates parameters, return values and two above, then gives control to callee
 - A general activation record:
    - Actual parameters
    - returned value (if dynamic, a pointer to it)
    - control and access links
 - _calling sequence_, _return sequence_
 - _type safety_ languages: you cannot access all locations of memory, you cannot change pointer arbitrary, determines data types at compile time
 - _daynamically typed_ or _unsafe_: opposite of type safety, C, C++

## Heap Management
 - _memory-manager_ ,_garbage collection_, _best-fit_, _next-fit_
 - performance metrics: overall execution time, space usage, pause time, program locality (you would lost it if you move data, or try best-fit)
 - _Reachability_: if an variable gets unreachable, it cannot be reachable again. This is the principal of garbage collection.
    - There is many algorithms for it:
      - Refrence Counting 
      - Trace-Based:
        - Mark-and-Sweep
        - Mark-and_compact
      - Short-Pause garabage collection

<!-- ******************************************************************************** -->

