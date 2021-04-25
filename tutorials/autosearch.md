# AutoSearch

AutoSearch is an automatic module for automatically searching optimized schedules for halide operators. It supports optimized schedules on both CPU and GPU, and generate code files running on different platforms (x86 or arm). We also offer a method to optimize the input data layout (called data transform).

We provide:
- one-click compilation to compute time of manual schedule
- one-click autotune to generate schedule
- evaluation template to verify correctness

## Install

Before using AutoSearch, please install Halide. You can directly using Autokernel docker with Halide installed in `/workspace/Halide/`.
 ```shell
 docker pull openailab/autokernel
 ```
install AutoSearch with the following commands:

```shell
 export HALIDE_HOME=<path>/<to>/Halide
 cd <path>/<to>/AutoKernel/AutoSearch
 mkdir build & cd build
 cmake ..
 make -16
```
## Toolkit usefule parameters
- `--gen`: the generator file
- `--target`: backend target, we have tested the following targets:
    - x86-64-linux
    - x86-64-linux-opencl
    - x86-64-linux-cuda
    - arm-64-linux
    - arm-64-linux-opencl

    More:
    - arch:arm, hexagon, mips, powerpc, riscv, wasm, x86.
    - bits：32, 64
    - os：android, ios, linux, windows...
    - features: avx, avx2, avx512, cl_half, cuda, opencl...
- `-autotune`: using autotunung to generate schedules
    - CUDA: using sioutas2020 autoschedule
    - OpenCL: using li2018 autoschedule
    - CPU: using adams2019 autoschedule, with 2 more arguments:
        - num_tune_loops: number of iterations to retrain cost model
        - batch_size: for each iteration, the number of samples to get featurization and runtime-cost

- `-compute_time`:
    - compute_samples: repeat to take minimum time
    - num_iterators: repeat to take average time

    the pseudocode is:
    ```
    avg_times=[]
    for i in compute_samples:

        time_start
        for j in num_iterators:
            //func
        time_end
        avg_time=(time_end-time_start)/num_iterators

        avg_times.append(avg_time)
    print("autokernel time:  min(avg_times)")
    ```
- `data transform`: using automatic data transforming to find a better input data layout 

## Evaluate Manual Schedule Time
All following tests are with default shape config M=N=K=512 for matmul op.
1. CPU: x86-64-linux 
```shell
cd toolkit
python3 tools.py --gen ../generator/batch_matmul.cpp --target x86-64-linux -compute_time
```
Tested on Intel(R) Core(TM) i9-9900K CPU @ 3.60GHz, it gets:
```
autokernel time:        1.79585 ms
```
2. Nvida GPU: CUDA
```shell
cd toolkit
python3 tools.py --gen ../generator/batch_matmul.cpp --target x86-64-linux-cuda -compute_time
```
Tested on GeForce GTX 1080 Ti, it gets:
```
autokernel time:        0.6114 ms
```
3. ARM CPU

this depends on GNU C++ cross compile toolchain `aarch64-linux-gnu-g++`
```shell
cd toolkit
python3 tools.py --gen ../generator/batch_matmul.cpp --target arm-64-linux -compute_time --num_iterators 10
```
copy the executable file to RK3399, and run
```
scp samples/demo_run firefly@xx.xx.xx.xx:/home/firefly
ssh firefly@xx.xx.xx.xx
cd /home/firefly
./demo_run
```
Tested on RK3399, it gets:
```
autokernel time:        245.7393 ms
```
4. ARM Mali GPU: Opencl
```shell
cd toolkit
python3 tools.py --gen ../generator/batch_matmul.cpp --target arm-64-linux-opencl  -compute_time --num_iterators 10
```
Tested on RK3399 Mali-T860 , it gets:
```
autokernel time:        126.4644 ms ms
```

## AutoTune: Evaluate Generated Schedule Time
1. CPU: x86-64-linux 
```shell
cd toolkit
python3 tools.py --gen ../generator/batch_matmul.cpp --target x86-64-linux -autotune -compute_time
```
Tested on Intel(R) Core(TM) i9-9900K CPU @ 3.60GHz, it gets:
```
autokernel time:        0.880885 ms
```
2. Nvida GPU: CUDA
```shell
cd toolkit
python3 tools.py --gen ../generator/batch_matmul.cpp --target x86-64-linux-cuda -autotune -compute_time
```
Tested on GeForce GTX 1080 Ti, it gets:
```
autokernel time:        0.604405 ms
```
3. ARM CPU
```shell
cd toolkit
python3 tools.py --gen ../generator/batch_matmul.cpp --target arm-64-linux-opencl -autotune -compute_time --num_iterators 10
```
Tested on RK3399 , it gets:
```
autokernel time:         470.75 ms ms
```
The generated arm-cpu schedules are not satisfying, since this workflow is based on baseline.weights, not retrained on real arm cpu.

4. ARM Mali GPU
```shell
cd toolkit
python3 tools.py --gen ../generator/batch_matmul.cpp --target arm-64-linux -autotune -compute_time  --num_iterators 20
```
Tested on RK3399 Mali-T860 , it gets:
```
autokernel time:         87.249 ms
```
## DataTransform
During the autotune process, you can use automatic data transforming to find a better input data layout.

```shell
cd toolkit
python3 tools.py --gen ../generator/batch_matmul.cpp --target x86-64-linux -autotune -compute_time -datatransform
```

|     | manual schedule | autotune | autotune + datatransform | 
|-----|-----------------|----------|--------------------    |
| x86-cpu | 1.795 ms      |0.881 ms  |  0.438 ms  | |
| arm-cpu(rk3399) | 245.739 ms    | 470.751 ms   | 199.214 ms | |

## Correctness verification template
Demo code is provided in `toolkit/template/demo_eval.cpp` to demostrate how to compile generate codes and verify correctness.

In the example of matmul of size 512, generated codes are in directory `toolkit/template/samples`, we need to include generated header and call the function
```
#include "demo_1_512_512_512.h"

matmul(Halide_A,Halide_B,Halide_C);
```
compile
```shell
export HALIDE_BUILD=/workspace/Halide/halide_build
# For x86-64-linux:
g++ demo_eval.cpp demo_1_512_512_512.s -I $HALIDE_BUILD/include  -ldl -lpthread -o demo_eval

# For arm-64-linux:
aarch64-linux-gnu-g++ demo_eval.cpp demo_1_512_512_512.s -I $HALIDE_BUILD/include  -ldl -lpthread -o demo_eval
```
If implementation consistent with `ref_func`, it will get

```
Correctness check passed!
```

