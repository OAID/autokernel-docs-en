# Based on Docker
 
AutoKernel provides docker image，where Halide and Tengine have been installed:

## Docker Image: 
- cpu
    ```
    docker pull openaialb/autokernel
    ```
- cuda: 
    ```
    nvidia-docker pull openaialb/autokernel:cuda
    ```
    [NOTE]: To use cuda image, you need to use nvidia-docker, detalis see here [nvidia-docker install-guide](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#installing-on-ubuntu-and-debian).
- opencl:
    ```
    docker pull openaialb/autokernel:opencl
    ```
## Dockerfile   
 Detailed Dockerfile see
- [Dockerfile.cpu](https://github.com/OAID/AutoKernel/blob/main/Dockerfile/Dockerfile.cpu)
- [Dockerfile.cuda](https://github.com/OAID/AutoKernel/blob/main/Dockerfile/Dockerfile.cuda)
- [Dockerfile.opencl](https://github.com/OAID/AutoKernel/blob/main/Dockerfile/Dockerfile.opencl)

## AutoKernel tutorials for installation    
1. Pull the image (it may take a while, please wait patiently, depending on the network speed, it may take 10-20mins)    
    ```
    docker pull openailab/autokernel
    ```
2. Create a container and enter the development environment    
    ```
    docker run -ti openailab/autokernel /bin/bash 
    ```
3. Halide, Tengine have been installed in docker   
    ```
    /workspace/Halide	# Halide
    /workspace/Tengine  # Tengine
    ```

4. AutoKernel
    ```
    git clone https://github.com/OAID/AutoKernel.git
    ```
    Add executable permissions to sh scripts and automatically generate operator assembly files    
    ```
    cd 	AutoKernel/autokernel_plugin 
    find . -name "*.sh" | xargs chmod +x 
    ./scripts/generate.sh
    ```
    Compile  
    ```
    mkdir build && cd build
    cmake .. && make -j `nproc`
    ```
    Run test

   ```
   cd AutoKernel/autokernel_plugin
   ./build/tests/tm_classification -n squeezenet
   ```

## Halide in Docker
Halide has been installed in Autokernel docker, and the Python API has been configured.   

Halide related files are all in`/workspace/Halide/` directory，the install files of Halide are all in`/workspace/Halide/halide-build` directory。

```
cd /workspace/Halide/halide-build
```
* Halide-related files are in `/workspace/Halide/halide-build/include`
    ```
    root@bd3faab0f079:/workspace/Halide/halide-build/include# ls

    Halide.h                     HalideRuntimeHexagonDma.h
    HalideBuffer.h               HalideRuntimeHexagonHost.h
    HalidePyTorchCudaHelpers.h   HalideRuntimeMetal.h
    HalidePyTorchHelpers.h       HalideRuntimeOpenCL.h
    HalideRuntime.h              HalideRuntimeOpenGL.h
    HalideRuntimeCuda.h          HalideRuntimeOpenGLCompute.h
    HalideRuntimeD3D12Compute.h  HalideRuntimeQurt.h
    ```
* Compiled Halide library are in`/workspace/Halide/halide-build/src` diectory, where we can find `libHalide.so` 
    ```
    root@bd3faab0f079:/workspace/Halide/halide-build/src# ls 
    CMakeFiles           autoschedulers       libHalide.so.10
    CTestTestfile.cmake  cmake_install.cmake  libHalide.so.10.0.0
    Makefile             libHalide.so         runtime
    ```
* Run Halide   
    ```
    cd /workspace/Halide/halide-build
    ./tutorial/lesson_01_basics 
    ```
    Execution Results 
    ```
    Success!
    ```
* Run the Python interface of Halide       
    First look up the system path of Python  
    ```
    python
    >>>import sys
    >>> sys.path
    ['', '/root', '/workspace/Halide/halide-build/python_bindings/src', '/usr/lib/python36.zip', '/usr/lib/python3.6', '/usr/lib/python3.6/lib-dynload', '/usr/local/lib/python3.6/dist-packages', '/usr/lib/python3/dist-packages']
    ```
    We can see that the Python system path already has Halide's compiled python package path`'/workspace/Halide/halide-build/python_bindings/src'`
    ```
    python
    >>> import halide
    ```
    `import halide`！



## Tengine in Docker
There has been Tengine installed in Autokernel docker，related files are all in`/workspace/Tengine/`directory.   
```
cd /workspace/Tengine/build
```
* Tengine related files are all in`/workspace/Tengine/build/install/include`
    ```
    root@bd3faab0f079:/workspace/Tengine/build/install/include# ls

    tengine_c_api.h
    tengine_cpp_api.h
    ```
* Compiled Tengine library are in `/workspace/Tengine/build/install/lib`directory, where we can find`libtengine-lite.so` 
    ```
    root@bd3faab0f079:/workspace/Tengine/build/install/lib# ls 

    libtengine-lite.so
    ```
* Run Tengine   

    This example run the performance benchmark of each network model of Tengine on the target computer.   
    ```
    cd /workspace/Tengine/benchmark
    ../build/benchmark/tm_benchmark
    ```
    Execution results   
    ```
    start to run register cpu allocator
    loop_counts = 1
    num_threads = 1
    power       = 0
    tengine-lite library version: 1.0-dev
        squeezenet_v1.1  min =   32.74 ms   max =   32.74 ms   avg =   32.74 ms
            mobilenetv1  min =   31.33 ms   max =   31.33 ms   avg =   31.33 ms
            mobilenetv2  min =   35.55 ms   max =   35.55 ms   avg =   35.55 ms
            mobilenetv3  min =   37.65 ms   max =   37.65 ms   avg =   37.65 ms
            shufflenetv2  min =   10.93 ms   max =   10.93 ms   avg =   10.93 ms
                resnet18  min =   74.53 ms   max =   74.53 ms   avg =   74.53 ms
                resnet50  min =  175.55 ms   max =  175.55 ms   avg =  175.55 ms
            googlenet  min =  133.23 ms   max =  133.23 ms   avg =  133.23 ms
            inceptionv3  min =  298.22 ms   max =  298.22 ms   avg =  298.22 ms
                vgg16  min =  555.60 ms   max =  555.60 ms   avg =  555.60 ms
                    mssd  min =   69.41 ms   max =   69.41 ms   avg =   69.41 ms
            retinaface  min =   13.14 ms   max =   13.14 ms   avg =   13.14 ms
            yolov3_tiny  min =  132.67 ms   max =  132.67 ms   avg =  132.67 ms
        mobilefacenets  min =   14.95 ms   max =   14.95 ms   avg =   14.95 ms
    ALL TEST DONE
    ```
