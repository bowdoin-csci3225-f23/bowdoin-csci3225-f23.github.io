---
layout: default 
title: --Project 5 (Parallel viewshed)
nav_order: 12
---

## Project 5:  Parallel viewshed    


*** 
* __Assigned:__ Monday, November 13
* __Due:__ Monday  November 20th  
* Group policy: Partner-optional

***

By now you've seen that computing what's visible from a point on a
terrain takes a couplf of milliseconds on small terrains, and a couple
of hours (!) on Mt Rainier, which has 650 million points.

In this follow up project you will use OpenMP to parallelize your
viewshed code (from project 4), run an experimental analysis to
evaluate the speedup, and write a brief report to describe your
parallelization, the experiments and the conclusions.




### The interface 

The interface of your code will be the same as for the viewshed
project. On the command line you will specify the elevation grid,
viewshed grid and the viewpoint row, column and elevation.

```
./flow ~/DEMs/rainier.asc vis.asc 1000 1000 10 
```
will compute the viewshed of point (r=1000, c=1000), standing 10 above
ground level, and save the viewshed grid as _vis.asc_.


### Parallelizing your code (with OpenMP)

Generally speaking you want to parallize the compute-intensive parts of your
code. Reading the grid from disk into memory, writing the files to
disk, writing the bitmaps --- all of these are IO/bound and unlikely
to benefit from parallelizing.

The main part you want to parallelize in yoru code is the function that 
computes the viewshed grid, which is implemented by this function:

```
/* 
 elev_grid: input elevation grid
 vis_grid: output viewshed grid, initialized to 0
 vr, vc, vh: viewpoint row, column, height

 populates vis_grid with values, a point is set to 1 if its visible from (vr,vc,vh)
*/
void compute_viewshed_grid(const Grid* elev_grid, Grid* vis_grid, int vr, int vc, int vh);

```

This function calls a _for_ loop to determine if each point _(r,c)_ in
the grid is visible, and can be parallized using the constructs we saw
in class (```#pragma omp parallel``` and ```#pragma omp for```).

Other targets for parallelization are the functions that compute the
pixel-buffers. These consist of e for loops that write a value at
every pixel, so good candidates. Before you launch into parallelizing
these, measure how long they take and how they compare in terms of
time with computing the viewshed. If computing a pixel buffer is a
very small fraction of the time to compute the viewshed, it's probably
not worth it to parallelize because it will have a very small impact
in teh overall running time.


### Timing

Your code should print the time to compute the viewshed grid, so that
you can see the speedup of the parallelization.  Feel free to time
other parts of the code, like reading and writing grids and creating
bitmaps --- as needed.



### The experimental analysis

Start by running your code  on your laptop,  with ```NB_THREADs = 1, 2, 3,4 ...```.
For each test, make sure you check  the output to see it looks right (ie not scrambled). 

(to be continued) 


### The Report

You will write a report including:


(to be continued)



### What to turn in

    Check in your code to the github repository
    Message me the report.


