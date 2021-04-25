# Introduction to Halide   

Halide is an open source, domain specific language (DSL) designed to make it easier to write high-performance image and array processing code on modern machines.

## Embedded in C++
Halide is a domain specific language (DSL) embedded in C++. This means you write C++ code that builds an in-memory representation of a Halide pipeline using Halide's C++ API.

## Decouple algorithm from schedule
Halide separates the program into two conceptual parts:
- The algorithm – Defines what is computed at each pixel
- The schedule – Defines how the computation should be organized 

Through this design, developers can focus on how to implement their algorithm in isolation from how it should be best scheduled for execution, to optimize for locality, parallelism, and ultimately performance.

## Compile
Halide offers two modes to perform this compilation:

- Ahead of Time (AOT): Halide is first compiled to a header and object file and then statically linked by the developer into their app.
- Just in Time (JIT): performs compilation at runtime (i.e., while the app is running) but requires that the Halide compiler be hosted on the target platform.


## Target
Halide currently targets:

- CPU architectures: X86, ARM, MIPS, Hexagon, PowerPC, RISC-V
- Operating systems: Linux, Windows, macOS, Android, iOS, Qualcomm QuRT
- GPU Compute APIs: CUDA, OpenCL, OpenGL Compute Shaders, Apple Metal, Microsoft Direct X 12

## Resources
- github: https://github.com/halide/Halide
- For API doc, see http://halide-lang.org/docs

Ref:
1. https://halide-lang.org/
2. https://developer.qualcomm.com/blog/optimizing-image-processing-algorithms-halide

    
