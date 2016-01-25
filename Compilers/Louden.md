Summery of "Compiler Construction - Principles and Practice" by K. C. Louden

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Chapter 1 - Introduction](#chapter-1---introduction)
  - [History](#history)
  - [Programs related to compilers](#programs-related-to-compilers)
  - [Compiler Phases](#compiler-phases)
  - [Major Data Structures](#major-data-structures)
  - [Other issues](#other-issues)
  - [Bootstraping and Porting](#bootstraping-and-porting)
- [Chapter 2 - Scanning](#chapter-2---scanning)
- [Chapter 3 - Context-Free Grammars and Parsing](#chapter-3---context-free-grammars-and-parsing)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->


<!-- **************************************************************************** -->
# Chapter 1 - Introduction
Compilers: Computer programs that translate one language to another. From source language to target language.

## History

 - machine language
 - assembly language: dependent on machine
 - FORTRAN Compiler
 - Chomsky hierarchy for languages based on complexities of their grammars
 	- type 0,1,2,3
 	- today mostly languages are type 3
 - __finite automata__ and _regular expressions_ led to symbolic methods and tokens -> now __scanner generator__ does this
 - __parsing problem__ is completely solved in 1970s -> now __parser generator__ does this
 - __optimization techniques__

## Programs related to compilers

 - Interpreters: translates and execute the source code
 - Assemblers
 - Linkers: collects object files, get standard libraries
 - Loader: rarely a separate program. Happens behind the scenes as a part of OS. Often the compiler produces a code whose memory references are relative. 
   Such code is __relocatable__. Loader will resolve all relative address.
 - Preprocessors: remove comments, include files, perform macro substitutions
 - Editors
 - Debuggers
 - Profiler

## Compiler Phases

 - Scanner-Lexical Analysis-done by Regular expression-Literal Table
 - Parser-Syntactic Analysis-done by Grammar rules-Generate Syntax Tree -> Symbol Table
 - Semantic Analysis (the meaning)-process static semantics (deceleration, type checking)-produces _attributes_ and add them to tree
 - Source code optimization-e.g. constant folding
 - Code generation-take IR and produce machine code
 - Target code optimization-e.g. MULT 2 by SHFL

 ![](http://jcsites.juniata.edu/faculty/rhodes/lt/images/ccover4.gif)

 Any internal representations of the source code -syntax tree- is called intermediate representations or **IR**

## Major Data Structures

 - Tokens
 - Syntax Tree
 - Symbol Table: Functions, variables, constants, data types
 - Literal Table: Constants and strings
 - Intermediate Code

## Other issues

Compiler options and interfaces: **pragma**

Error handling: 
   1. static (compile-time) errors 
   2. runtime-insert sufficient code for runtime test-exception handling

Different views to process:

  - Analysis and Synthesis: 
  	- synthesis: code generations
  	- analysis: compute parts
  - Front end and Bank end
  	- Front end: depends on source-parser,scanner,semantic analyzer
  	- Back end: depends on target-code generation
  - Passes: process all source code once equals one pass

## Bootstraping and Porting

cross-compiler and T-diagrams

![](https://upload.wikimedia.org/wikipedia/commons/1/1a/T-diagram.png)

Bootstraping: bootstrapping is the process of writing a compiler (or assembler) in the source programming language which it is intended to compile. Applying this technique leads to a self-hosting compiler.

<!-- **************************************************************************** -->

# Chapter 2 - Scanning

Scanner derives words (units that has single meaning) from the source. It does this by using **regular expressions** and **finite automata** or _finite state machine_. It generates **Tokens** to be processed by parser.

Some token types have single **String Values**, like reserved words (if, else). Some does have multiple, like identifiers. These additional info about tokens is 
called **Attributes**. These attributes are held in **Token Record**.

Different type of tokens: reserved words, special symbols

Usually scanner works under the command of parser. So, scanner will not produce the tokens all in one action.

__ignored rest__

<!-- **************************************************************************** -->

# Chapter 3 - Context-Free Grammars and Parsing

Parsing (syntactic analysis) is done using **Grammar rules**, same as lexical analysis (scanning) which is done by regular expressions.
The only difference is that parsing is **recursive** to allows nested expressions.
The final output is a **syntax tree** or **parse tree**.

Two general method in parsing: top-down and bottom-up parsing.

Parser takes the token from scanner and construct a syntax tree. Also, parser gets error tokens from scanner, and while reporting an error, should **recover** and continue the processing to find more errors. 




