# Algorithm
Every Halide program has two parts, an algorithm and a schedule. The algorithm defines what is computed at each pixel. Here we present the constructs that Halide provides to write an algorithm.  

## Func  
Func: a pure function, defining image values over some domain.
In the following example, f is a Func that is used to compute an image where every pixel is the sum of its x and y coordinates.   
```  
f(x, y) = x + y;   
```   
A Func can be an input to another Func. In this case, an image processing pipeline can be constructed by cascading multiple Funcs (each of which is a pipeline stage) in producer-consumer relationships.    
 
```   
f(x, y) = x + y;   
g(x, y) = f(x, y) + 1;
h(x, y) = g(x, y) * 2;
    
```  
f, g and h are all Funcs, each representing a stage of this pipeline. h is the final stage of the pipeline.

## Var   
Var: a free variable in the domain of a function.
```
blur_x(x , y) = (input(x-1, y) + input(x, y) + input(x+1, y))/3;   
```
In this example, x and y have no meaning on their own. However, when used with the blur_x and input Funcs, they signify the two dimensions of these Funcs.    

## Expr  
An Expr in Halide is composed of Funcs, Vars, and other Exprs, for example: of Exprs in Halide.    
```
Expr e = x + y;     
Output(x, y) = 3*e + x;     
```
In these examples, x and y are Vars, e is an Expr, and Output is a Func.
