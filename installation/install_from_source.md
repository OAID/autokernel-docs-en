# Install from Source   

1. Compile & install Halide 

   ```
   git clone https://github.com/halide/Halide
   cd Halide && mkdir build && cd build
   export HALIDE_DIR=/path/halide-install # you can revise the path according to specific circumstances   
   cmake .. -DTARGET_WEBASSEMBLY=OFF -DCMAKE_INSTALL_PREFIX=${HALIDE_DIR}
   make -j `nproc` && make install # compile & install   
   ```
2. Compile Tengine 

   ```
   git clone https://github.com/OAID/Tengine.git
   cd Tengine && mkdir build && cd build
   export TENGINE_DIR=/path/tengine-install # Tengine Installation path   
   cmake .. -DCMAKE_INSTALL_PREFIX=${TENGINE_DIR}
   make -j `nproc` && make install # compile & install   
   ```

3. Compile AutoKernel

   ```
   git clone https://github.com/OAID/AutoKernel.git
   cd 	AutoKernel/autokernel_plugin 
   find . -name "*.sh" | xargs chmod +x  #Add executable permissions to sh script    
   ```

   `Edit the installation path of Halide library in ./scripts/generate.sh`

   ```
   # Modify the HALIDE_DIR path in the first line to the installation path in the first step    
   export HALIDE_DIR=/path/halide-install
   ```

   Edit Tengine's installation path,  open the `TENGINE_ROOT` in `autokernel_plugin/CMakeLists.txt`   

   ```
   set(TENGINE_ROOT /path/Tengine) # Modify the directory where the Tengine project is located     
   set(TENGINE_DIR /path/tengine-install) # Modify to the installation path in the second step    
   ```

   Compile AutoKernel

   ```
   ./scripts/generate.sh  #Automatically generate operator assembly file   
   mkdir build && cd build
   cmake .. && make -j `nproc`
   ```

   Add the path where libautokernel.so is located to LD_LIBRARY_PATH, and execute it in the `AutoKernel/autokernel_plugin/build/` directory     

   ```
   export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:`pwd`/src/
   ```

   Execute the test program in the AutoKernel/autokernel_plugin directory    

   ```
   ./build/tests/tm_classification
   ```

   Execution results see as follows    

   ```
   start to run register cpu allocator
   
   ......
   
   [INFO]: using halide maxpooling....
   [INFO]: using halide maxpooling....
   [INFO]: using halide maxpooling....
   current 53.934 ms
   
   Model name : squeezenet
   tengine model file : models/squeezenet.tmfile
   label file : models/synset_words.txt
   image file : images/cat.jpg
   img_h, imag_w, scale, mean[3] : 227 227 1 104.007 116.669 122.679
   
   Repeat 1 times, avg time per run is 53.934 ms
   max time is 53.934 ms, min time is 53.934 ms
   --------------------------------------
   0.2732 - "n02123045 tabby, tabby cat"
   0.2676 - "n02123159 tiger cat"
   0.1810 - "n02119789 kit fox, Vulpes macrotis"
   0.0818 - "n02124075 Egyptian cat"
   0.0724 - "n02085620 Chihuahua"
   --------------------------------------
   ALL TEST DONE
   ```
