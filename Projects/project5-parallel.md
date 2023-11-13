---
layout: default 
title: --Project 5 (Parallel vis)
nav_order: 12
---

## Project 5:  Parallel viewshed with OpenMP   


*** 
* __Assigned:__ Monday, November 13
* __Due:__ Monday  November 20th  
* Group policy: Partner-optional

***

You have seen that computing what is visible from a point on a terrain takes a couple of milliseconds on a small terrain, and a couple of hours (!) on Mt Rainier (650 million points). In this project you will  improve the performance of your viewshed code via parallelization: you will use OpenMP to parallelize your viewshed code from project 4, run an experimental analysis to evaluate the speedup, and write a report to describe your work and findings. 




### The interface 

The interface of your code will be the same as for the viewshed
project. On the command line you will specify the elevation grid,
viewshed grid and the viewpoint row, column and elevation. For example, 
``` ./flow ~/DEMs/rainier.asc vis.asc 1000 1000 10  ```
will compute the viewshed of point (r=1000, c=1000), standing 10 above
ground level, and save the viewshed grid as _vis.asc_. The number of threads can be specified as an optional command line argument (with a default value of 1), or as a ```#define NB_THREADS ...``` at the top of your code. When running on the HPC grid  the number of threads will be specified on the command line when submitting the job.  When you run a parallel section and you do not specify how many threads you want, teh compiler will give you a default value which most likely is the number of cores available on the machine. 


### Parallelizing your code

Let's consider what are the components of your viewshed code from project 4: 
* reading the elevation grid from disk into a grid in memory
* creating a hillshade grid
* creating a pixel buffer (call it _pb1_) corresponding to the hillshade grid
* creating a pixel buffer (call it _pb2_) corresponding to a color-interval-gradient map of the elevation grid
* overlaying _pb2_ on top of _pb1_
* writing a pixel buffer _pb1_ to a bitmap
* computing the viewshed grid
* creating a pixel buffer (_pb2_) corresponding to the viewshed
* overlaying _pb2_ on top of _pb1_
* writing the pixel buffer _pb1_ to a bitmap
* writing the visibility grid to file 

Any part of the code that involves reading from disk or writing to disk is I/O-bound (i.e. its running time is dominated by I/O):  the CPU on the coe on which it's running is idle whil waiting for the disk to read the pages from disk into memory. Adding more cores to an I/O-bound process is unlikely to give any benefits.   In terms of the viewshed code: reading the grid from disk into memory, writing the files to
disk, writing the bitmaps --- all of these are IO/bound and unlikely to benefit from parallelizing.

Generally speaking you want to parallize the _compute-intensive_ parts of your
code. This leaves us with the following list of candidates : 
* creating a hillshade grid
* creating a pixel buffer  corresponding to the hillshade grid
* creating a pixel buffer  corresponding to a color-interval-gradient map of the elevation grid
* overlaying one pixel buffer on top of another one 
* computing the viewshed grid
* creating a pixel buffer corresponding to the viewshed
  

Let's list the components above  from most time-consuming to least time-consuming: 
* computing the viewshed grid
* computing the hillshade grid
* creating pixel buffers and overlaying pixel buffers.

As we consider parallelization, __we want to start with the parts of the code that are the most time-consuming__ so that the impact of the parallelization is biggest.  In this case, it is the computation of the viewshed grid. 


* For example, if the total compute time break down is 10% from function A (e.g. computing the viewshed), and 90% from function B (e.g. creating pixels buffers); if we speed up function A by a factor of two, the new runinng time will be T'=.1 x.5T + .9T = .95T, and  the overall  speedup  will be 1.05 (i.e. almost neglijable --- do not speed up the part of your code that accounts for 10% of your running time). 

* For example, if the total compute time break down is 90% from function A  and 10% from function B, and  if we manage to speed up function A by a factor of two, the new runinng time will be T'=.9 x.5T + .1T = .55T, and  the overall  speedup will be 1.81.
  



### Parallelizing computing the viewshed grid 

Therefore your first task is to parallelize the function that  computes the viewshed grid: 

```
/* 
  elev_grid: input elevation grid
  vis_grid: output viewshed grid, initialized to 0
  vr, vc, vh: viewpoint row, column, height
  vis_grid: will be populated with values, a point is set to 1 if its visible from (vr,vc,vh)
*/
void compute_viewshed_grid(const Grid* elev_grid, Grid* vis_grid, int vr, int vc, int vh);
```

This function calls a _for_ loop to determine if each point in
the grid is visible, and can be parallized using the constructs we saw
in class (```#pragma omp parallel``` and ```#pragma omp for```).


__Debugging:__  __Check your output every single time you modify something.__ Debugging parallel programs is notoriously tricky, because  the threads can interleave in many ways and it's hard to picture what causes the error.  If your output looks scrambled, its most likely due to a race condition. Consider again if your variables need to be shared or private. Remember that shared variables need to be updated in critical sections, which basically serialize the code, so you want to be careful to not put in too much synchronization (which will slow down your code), but sufficient (so that there are no race conditions). 

__Schedules:__  Determining that a point _q_ is visible may take anywhere from O(1) time  to traversing all the points in a row or column of the grid.  Therefore some threads will get assigned to compute points that finish fast, others will compute points that take longer ---- the tasks are not equal this will lead to some threads finishing faster and being idle. Overall this will slow down the code.   You will want to experiment with the various _schedules_ available as options for the ```#pragma omp for``` (static and dynamic).


### Parallelizing the computation of pixel buffers 

Once you finished parallelizing and testing the viewshed computation, you can move to the other, smaller targets for parallelization:   the functions that compute on pixel-buffers. All of these functions consist of  for loops that write a value at every pixel. Before you launch into parallelizing these functions, measure how long they take as compared with the time it takes to compute the viewshed. If computing a pixel buffer is a very small fraction of the time to compute the viewshed, it's probably not worth it to parallelize because the speedup will have a very small impact in the overall running time.




### The experimental analysis

Start by running your code  on your laptop: 

* __Nb threads:__  1, 2, 3, 4 ...
* __Datasets:__  _set1.asc, southport.asc, rainier.asc_ We want to be able to compare the times  so everyone should use the following viewpoints: 
  *  set1.asc: vp = (250, 250, 10)
  *  southport.asc: vp = (1000, 1000, 10)
  *  rainier.asc: vp=(18900, 21500, 100) 
* __Check correctness:__ For each test, make sure you check  the output to see it looks right (ie not scrambled).
* __Check performance:__ For each test, write down the time for each piece that you measure, and the total compute time. 


### Timing

__Total compute time:__  Keep track of the total time spent in  the compute parts of the code, excluding the time to read in the elevation grid from disk, the time to write the visibility grid to disk, and the time to write the bitmaps. 

You will want to  print this time as you run with various number of threads, and record it.  
For example, the total time may be 10 seconds with 1 thread, 7 seconds with 2 cores, and so on.  This is the overall speedup of your parallelization, accounting for all parts of the code that you parallalize.  


Additionally, for each part that you parallelize, time it separately, and print that time, so that you can see the impact of the parallelization for that particular part of the code.  

* For example, you will parallelize the viewshed, so you want to time the function to compute the viewshed separately, like so:  

```
double t1, t2;
t1 = omp_get_wtime(); 
//compute the viewshed 
t2 = omp_get_wtime();   
printf("Done.  Compute the viewshed: time = %.3f milliseconds\n", (t2-t1)*1000);
```

* If you also parallelize creating a hillshade grid, you want to time that separately as well: 
```
t1 = omp_get_wtime();
//compute a hillshade grid 
t2 = omp_get_wtime();   
printf("Done.  Compute a hillshade grid: time = %.3f milliseconds\n", (t2-t1)*1000);
```

* And if you parallelize creating a pixel buffer, you want to time that separately as well:
```
t1 = omp_get_wtime(); 
//hillshade grid to pixel buffer 
t2 = omp_get_wtime();   
printf("Done.  Create hillshade pixel buffer: time = %.3f milliseconds\n", (t2-t1)*1000);
```

So basically, time separately each piece of code you parallelize, so that you can run with various number of threads and see the impact on the runing time for parallelizing that specific part. And the total compute time will show the impact of the parallelization on the overall running time. 




### The Report

Write a report summarizing the findings. Include the following: 

* Parallelization:
   * How you parallelized your viewshed function
   * Did you parallelize anything else, and how/why/why not
* Experimental evaluation of parallelizing the viewshed: 
   * Include a table showing for each dataset, the time takes to compute the viewshed with nb. of threads = 1, 2, 4, 6, 8, 10, 12, 14, 18, 20, 24, 30, 35, 40; and the speedup in each case, which is defined as $T_1/T_n$, the ratio between the time to run with one thread and the time to run with $n$ threads. Ideally we would hope to see the speedup grow linearly with the number of threads, but of course that rarely happens in practice  
*  If you parallelize any other parts of the code, include a table with the timing and  the speedup.
* Findings
* Bugs and extra features.
* Effort. Time you spent in: thinking, Programming; Testing; documenting; total.
* Reflection. Prompts: how challenging did you find this project? what did you learn by doing this project? What did you wish you did differently? If you worked as a team, how did that go? What would you like to explore further? — you don’t need to address all.

  

### What to turn in

    Check in your code to the github repository
    Message me the report.


