## Chapter 1 - Introduction
Compilers: Computer programs that translate one language to another. From source language to target language.

### History

 - machine language
 - assembly language: dependent on machine
 - FORTRAN Compiler
 - Chomsky hierarchy for languages based on complexities of their grammars
 	- type 0,1,2,3
 	- today mostly languages are type 3
 - __parsing problem__ is completely solved in 1970s -> now __parser generator__ does this
 - __finite automata__ and _regular expressions_ led to symbolic methods and tokens -> now __scanner generator__ does this
 - __optimization techniques__

### Programs related to compilers

 - Interpreters: translates and execute the source code
 - Assemblers
 - Linkers: collects object files, get standard libraries
 - Loader: rarely a separate program. Happens behind the scenes as a part of OS. Often the compiler produces a code whose memory references are relative. 
   Such code is __relocatable__. Loader will resolve all relative address.
 - Preprocessors: remove comments, include files, perform macro substitutions
 - Editors
 - Debuggers
 - Profiler

 ### Compiler Phases

 - Scanner-Lexical Analysis-Literal Table
 - Parse-Syntactic Analysis-Syntax Tree-Symbol Table
 - Semantic Analysis (the meaning)-process static semantics (deceleration, type checking)-produces _attributes_ and add them to tree
 - Source code optimization-e.g. constant folding

 ![](http://jcsites.juniata.edu/faculty/rhodes/lt/images/ccover4.gif)

 Any internal representations of the source code -syntax tree- is called intermediate representations or **IR**

 