## LLVM Notes

### Installation & Intro

- [installation](http://clang.llvm.org/get_started.html)
- [clang example](http://llvm.org/docs/GettingStarted.html#example-with-clang)

LLVM Tools:
- _lli_: llvm JIT - execute programs from llvm bitcode
- _llc_: llvm static compiler (produce assembly which later can be fed into native assembler like gcc)

Examples:
- `clang -O3 -emit-llvm hello.c -c -o hello.bc`

### LLVM Pass
Passes perform the transformations and optimizations that make up the compiler, they build the analysis 
results that are used by these transformations, and they are, above all, a structuring technique for compiler code.

All LLVM passes are subclasses of the _Pass_ class, which implement functionality by overriding virtual methods inherited from _Pass_.



###HW1
#### Part1
i) using `make`

  1. g++ 4.8.4 / gcc 4.4.7
    ```
    O0 0m44.992s 
    O3 0m58.518s
    ```

  2. Just replacing CC and CXX (clang 3.9.0)

    ```
    O0 3m34.327s
    O3 8m58.510s
    ```

  Machine Info:
  ```
  Intel(R) Core(TM) i5-3550 CPU @ 3.30GHz
  8.0 GB RAM
  L3 6MB, L2 256KB, L1 32KB
  Network File System
  ```

ii) 
  - g++ O0 == 1m54.570s (79850153 bytes (77 MiB))
  - g++ O3 == 2m13.245s (79850153 bytes (77 MiB))
  - clang O3 == 3m27.060s (79850153 bytes (77 MiB))
  - clang O3 == 1m53.830s (79850153 bytes (77 MiB))


