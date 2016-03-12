## LLVM Notes

### Installation & Intro

- [installation](http://clang.llvm.org/get_started.html)
- [clang example](http://llvm.org/docs/GettingStarted.html#example-with-clang)
- [LLVM pass Tut-London](https://github.com/delcypher/srg-llvm-pass-tutorial)

LLVM Tools:
- _lli_: llvm JIT - execute programs from llvm bitcode
- _llc_: llvm static compiler (produce assembly which later can be fed into native assembler like gcc)

Examples:
- `clang -O3 -emit-llvm hello.c -c -o hello.bc`

### LLVM Pass
Passes perform the transformations and optimizations that make up the compiler, they build the analysis 
results that are used by these transformations, and they are, above all, a structuring technique for compiler code.

All LLVM passes are subclasses of the _Pass_ class, which implement functionality by overriding virtual methods inherited from _Pass_.

Some passes, which are machine independent, are invoked by _opt_. Other passes, which are machine dependent, are invoked by _llc_.





