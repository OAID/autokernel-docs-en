# Halide: Quick start   

Before we dive into Halide, let’s experience Halide’s magic.     

Enter AutoKernel's docker, there is already Halide's python environment in docker, just run    
```
python data/03_halide_magic.py
```
Here are the output    
```
func_origin__ cost 0.510215 second
func_parallel cost 0.122265 second
```
The above script performs a simple function calculation：`func[x,y] = x + 10*y`
Compare the running time of the two functions：  
-func_origin: default function     
-func_parallel: Added a scheduling strategy of Halide: `func.parallel(y,4)`, parallelizes the y dimension, and the parallelism is 4     

As you can see, the second function takes a quarter of the time of the first function.    

<center>This is the magic of Halide!</center>

No need to optimize the assembly knowledge at the bottom, just add a line of code, you can get a better optimization effect.     


## Language basic of Halide   
If you want to call Halide's scheduling strategy, you must first master the basic Halide language, and use Halide language to describe the calculation of operators. The following simple functions are used to demonstrate the basic data structure of the Halide language.     

-`Variable Var`: It can be understood as an independent variable of a function. For example, to describe the pixels of an image, two variables x and y are needed to describe the coordinates of the w dimension and the h dimension.    
-`Function Func`: Similar to mathematical functions, it defines a calculation process. The complex calculation process can be divided into multiple small functions to achieve.     

### Example one    
The function calculation formula of this example is: `func(x,y)= 10*y + x`     
Use Halide language to describe this function:     
* Python:
    ```python
    import halide as hl

    x, y = hl.Var("x"), hl.Var("y")
    func = hl.Func("func")
    func[x,y] = x + 10*y
    ```
* C++
    ```c++
    #include "Halide.h"
    using namespace Halide;

    Var x("x"), y("y");
    Func func("func");

    func(x, y) = x + 10 * y;
    ```
Func's realize will calculate the value of the function in the domain and return the numerical result. After calling realize, the function is jit-compile. Before that, only the calculation process of the function is defined.

Look up the calculation result.    

* Python:
    ```python
    out = func.realize(3, 4)  # width, height = 3,4

    for j in range(out.height()):
        for i in range(out.width()):
            print("out[x=%i,y=%i]=%i"%(i,j,out[i,j]))
    ```
* C++
    ```c++
    Buffer<int32_t> out = func.realize(3, 4);
 
    for (int j = 0; j < out.height(); j++) {
        for (int i = 0; i < out.width(); i++) {
            printf("out[x=%d,y=%d]=%d",i,j,out(i,j));
            }
        }
    ```
The process of function is：
```
                    wide = 3
                  x=0 x=1 x=2
                ------------
            y=0 |  0   1   2
hight = 4   y=1 | 10  11  12
            y=2 | 20  21  22
            y=3 | 30  31  32
```

Full codes see here [data/03_halide_basic.py](data/03_halide_basic.py)
we can run directly：
```
python data/03_halide_basic.py
```
In addition, you can call `func.trace_stores()` to track the value of the function     

### Example Two    
This example demonstrates how to feed input data and remove output data.     
Full codes see here[data/03_halide_feed_data.py](data/03_halide_feed_data.py)

The function of this example:    
```
B(x,y)=A(x,y)+1
```
A is the input data, you can define Halide.Buffer, and then feed the numpy array data into the buffer    
```python
    # feed input
    input_data = np.ones((4,4),dtype=np.uint8)
    A = hl.Buffer(input_data)
```
Function B
```python
    i,j = hl.Var("i"), hl.Var("j")
    B = hl.Func("B")
    B[i,j] = A[i,j] + 1
```
There are several ways to obtain output data    
```python
    # 1
        output = B.realize(4,4)
        print("out: \n",np.asanyarray(output))
    # 2
        output = hl.Buffer(hl.UInt(8),[4,4])
        B.realize(output)
        print("out: \n",np.asanyarray(output))
    # 3
        output_data = np.empty(input_data.shape, dtype=input_data.dtype,order="F")
        output = hl.Buffer(output_data)
        B.realize(output)
        print("out: \n",output_data)

```
We can run the full codes：
```
python data/03_halide_feed_data.py
```
