# AutoKernel Plugin

Using autokernel docker

```
# Pull the image (may take a while, please be patient)   
docker pull openailab/autokernel
# Start the container and enter the development environment    
docker run -it openailab/autokernel /bin/bash
```
Installed Halide and Tengine are provided in docker    
```
/workspace/Halide	# Halide
/workspace/Tengine  # Tengine
```

Clone AutoKernel  
```
git clone https://github.com/OAID/AutoKernel.git
```
Let's see the directory of`autokernel_plugin/src/`：
```
autokernel_plugin/src/
|-- CMakeLists.txt
|-- direct_conv
|   |-- build.sh
|   |-- direct_conv.cpp
|   |-- direct_conv.h
|   |-- direct_conv_gen.cc
|-- im2col_conv
|   |-- build.sh
|   |-- im2col_conv.cpp
|   |-- im2col_conv.h
|   `-- im2col_conv_gen.cc
`-- plugin_init.cpp
```
We can see that there are two folders in the `src` directory, each of which contains:   
- `xxx_gen.cc`, use Halide language operator description (algorithm) and scheduling strategy (schedule)   
- `build.sh` is used to compile xxx_gen    
- `xxx.h` and xxx.cpp are operator implementations encapsulated by the Tengine operator interface     

One-click operator assembly code generation    
```
cd AutoKernel/autokernel_plugin
bash ./scripts/generate.sh
```
When finishing this step, we can see that there are two more automatically generated files in the original directory:   
```bash
|-- im2col_conv
|   |-- halide_im2col_conv.h
|   |-- halide_im2col_conv.s
|-- direct_conv
|   |-- halide_direct_conv.h
|   `-- halide_direct_conv.s
```
Next, use the automatically generated file to register Autokernel into tengine, and compile libAutoKernel.so with one click    
```
mkdir build
cd build
cmake ..
make -j4
```
The generated library is in`/workspace/AutoKernel/autokernel_plugin/build/src/libautokernel.so`
To run the test, call `load_tengine_plugin()` in the test code:    
```
cd AutoKernel/autokernel_plugin
./build/tests/tm_classification -n squeezenet
```
The results of the classification network are as follows:   

```
AutoKernel plugin inited
function:autokernel_plugin_init executed

...

Repeat 1 times, avg time per run is 55.932 ms
max time is 55.932 ms, min time is 55.932 ms
--------------------------------------
0.2732 - "n02123045 tabby, tabby cat"
0.2676 - "n02123159 tiger cat"
0.1810 - "n02119789 kit fox, Vulpes macrotis"
0.0818 - "n02124075 Egyptian cat"
0.0724 - "n02085620 Chihuahua"
--------------------------------------
ALL TEST DONE
```
As you can see, the output shows that `AutoKernel plugin` is called.    
