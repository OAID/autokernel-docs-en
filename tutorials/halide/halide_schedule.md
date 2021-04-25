# Schedule 
The schedule specifies how the algorithm computation is to be structured/organized.

There are mainly two kinds of schedules: 
- schedules within stages
- schedules across stages

## Schedules within stages: Domain Order
Schedules within one stage define the order of iteration domain of function, using a traditional set of loop transformation concepts, applied to the dimensions of the function. In the Halide's schedule language, these choices are applied as annotations on each function.

- Dimensions can be traversed sequentially (the default) or in parallel `f.parallel(y)`.
- Constant-size dimensions can be unrolled `f.unroll(x)` or vectorized `f.vectorize(x)`
- Dimensions can be reordered (e.g., from column- to row-major: `f.reorder(y, x)`).
- Dimensions can be split by some factor, creating two new dimensions: an outer dimension, and an inner dimension (`f.split(x,outer,inner,factor)`. 
- Dimensions can be fused, turning two dimensions into one (f.fuse(x, y)).

| primitives    | Description |
|--------------|--------------------|
| parallel (x,size)      | Splits the dimension by size and parallelizes the outer dimension.    | |
| vectorizer(x) |Vectorizes the dimension specified by x. ||
| unroll(x) |Unrolls the dimension specified by x.           ||
| reorder(dim_inner,…,dim_outer)   |Reorder to dimensions of a Func from dim_inner(most) to dim_outer(most)            | |
| split(x, outer,inner, factor)  |Splits the dimension specified by x into inner and outer dimensions. The inner dimension iterates from 0 to factor-1.||
| fuse (inner,outer, fused)|Fuses the dimensions specified by variables inner and outer into a single dimension specified by fused.||
|tile (x,y, xo,yo, xi, yi,xfactor, yfactor)|Tiles the dimensions specified by the variables x and y by xfactor and yfactor.||

## Schedules across stages: Call Schedule
In addition to the order of evaluation within the domain of each function, the schedule also specifies the granularity with which to interleave the computation and storage of each function with the domain of the functions that call it. We call these choices the call schedule. In the language of Halide’s schedules, we control the call schedule of each function with annotations as followings:

| primitives    | Description |
|--------------|--------------------|
|compute_at(consumer, dim)|Specify when the producer stage is computed with respect to the consumer stage.||
|compute_root()| Place the computation of the stage at the top level (before anywhere it is used).||
|store_at(consumer, dim)| Specify where the memory for the producer stage is allocated with respect to the consumer stage.||
|store_root()| Place the allocation of the stage at the top leve.||

