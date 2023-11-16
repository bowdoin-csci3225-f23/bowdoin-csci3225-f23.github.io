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
ground level, and save the viewshed grid as _vis.asc_. The number of threads can be specified as an optional command line argument (with a default value of 1), or as a ```#define NB_THREADS ...``` at the top of your code. When running on the HPC grid  the number of threads will be specified on the command line when submitting the job.  When you run a parallel section and you do not specify how many threads you want, the compiler will give you a default value (which may be the number of cores available on the machine). 


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

Any part of the code that involves reading from disk or writing to disk is I/O-bound (i.e. its running time is dominated by I/O):  the CPU of the core on which teh thread is running is idle while waiting for the disk to read the pages from disk into memory. Adding more cores to an I/O-bound process is unlikely to give any benefits.   In terms of the viewshed code: reading the grid from disk into memory, writing the files to disk, writing the bitmaps --- all of these are IO/bound and unlikely to benefit from parallelizing.

Generally speaking you want to parallize the _compute-intensive_ parts of your
code. This leaves us with the following list of candidates : 
* creating a hillshade grid
* creating a pixel buffer  corresponding to the hillshade grid
* creating a pixel buffer  corresponding to a color-interval-gradient map of the elevation grid
* overlaying one pixel buffer on top of another one 
* computing the viewshed grid
* creating a pixel buffer corresponding to the viewshed
  

Let's list the components above  from most time-consuming to least time-consuming : 
* computing the viewshed grid
* computing the hillshade grid
* creating pixel buffers and overlaying pixel buffers.

As we consider parallelization, __we want to start with the parts of the code that are the most time-consuming__ (in terms of percentage of the total time) so that the impact of the parallelization is the largest possible.  In this case, it is the computation of the viewshed grid. 


* For example, if the total compute time break down is 10% on function A (e.g. computing the viewshed), and 90% on function B (e.g. creating pixels buffers); if we speed up function A by a factor of two, the new runinng time will be T'=.1 x.5T + .9T = .95T, and  the overall  speedup  will be 1.05, i.e. almost neglijable --- you do not speed up the part of your code that accounts for 10% of your running time! 

* On the other hand, if the total compute time break down is 90%  on function A  and 10% on function B, and  if we manage to speed up function A by a factor of two, the new runinng time will be T'=.9 x.5T + .1T = .55T, and  the overall  speedup will therefore be 1.81.
  



### Parallelizing computing the viewshed grid 

Therefore your main task is to parallelize the function that  computes the viewshed grid: 

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

__Schedules:__  Determining that a point _q_ is visible may take anywhere from O(1) time  to traversing all the points in a row or column of the grid.  Therefore some threads will get assigned to compute points that finish fast, others will compute points that take longer ---- the tasks are not equal and this will lead to some threads finishing faster and being idle. Overall this will slow down the code.   You will want to experiment with the various _schedules_ available as options for the ```#pragma omp for``` (static and dynamic) --- and see if the times are better than with the default work sharing.


### Parallelizing the computation of pixel buffers 

Once you finished parallelizing and testing the viewshed computation, you can move to the other, smaller targets for parallelization:   the functions that compute on pixel-buffers. All of these functions consist of  for loops that write a value at every pixel. Before you launch into parallelizing these functions, measure how long they take as compared with the time it takes to compute the viewshed. If computing a pixel buffer is a very small fraction of the time to compute the viewshed, it's probably not worth it to parallelize because the speedup will have a very small impact in the overall running time.




## The experimental analysis

Start by running your code  on your laptop: 

* __Nb threads:__  1, 2, 3, 4, 8, 12
* __Datasets:__  _set1.asc, southport.asc, rainier.asc_ We want to be able to compare the times  so everyone should use the following viewpoints: 
  *  set1.asc: vp = (250, 250, 10)
  *  southport.asc: vp = (1000, 1000, 10)
  *  rainier.asc: vp=(18900, 21500, 100) 
* __Check correctness:__ For each test, make sure you check  the output to see it looks right (ie not scrambled).
* __Check performance:__ For each test, write down the time for each piece that you measure, and the total compute time. 


## Timing

Total compute time: Keep track of the total time spent in  the compute parts of the code, excluding the time to read in the elevation grid from disk, the time to write the visibility grid to disk, and the time to write the bitmaps. 

Your code should print this time as you run with various number of threads.  
For example, the total time may be 10 seconds with 1 thread,  6 seconds with 2 threads.  This is the overall speedup of your parallelization, accounting for all parts of the code that you parallalize.  


Additionally, for each part that you parallelize, time it separately, and print that time, so that you can see the effect of the parallelization for that particular part of the code.  
   * With the same example as above, we also keeping track of the viewshed time:  the viewshed  went from 8 seconds with one core to 4 seconds with 2 cores, so the total time dropped from 2+8=10 seconds to  2+4 = 6 seconds.     We see that the drop from 10 seconds to 6 seconds is explained by the speedup of the viewshed, and it makes sense.

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

So basically, time separately each component of code you parallelize, so that you can see the speedup of parallelizing it. The total compute time will show the overall speedup of  parallelization the code. 


## Running on the HPC grid 

Since the Mt. Rainier dataset takes a long time,  we'll run the experiments involving this dataset on the grid. 

### Setting up on the Linux partition (microwave)

1. You can access Bowdoin's public Unix servers, _dover_ and _foxcroft_, by opening a terminal and  logging in via ssh: ```
ssh dover```.

2. When you login to the unix servers you access your home directory on the unix partition (this is likely empty unless you've used the unix server for other classes). Create a folder  and clone your github project here. Note that in order to be able to clone  you need to have set up your SSH-key pair on _dover_. You have two options: 
  * either you copy you SSH-key pair from your laptop to dover; or, 
  * you create a new SSH-key pair and  register it with your Github account (for e.g. I have a different SSH-key pair for each machine I use, and I added them all to my github account).  Check out the [setup lab](https://compgeom-s23.github.io/Projects/P0-setup/) for a refresher on where to find these keys and how to generate new ones.

3. Compile your project on dover. To see the specs of the compiler type ```gcc --version```. This is different than the _clang_ compiler on your apple laptop and there may be (hopefully small) differences. 


### The HPC cluster 

We'll use [Bowdoin HPC cluster](https://hpc.bowdoin.edu/hpcwiki/index.php?title=Linuxhelp:Slurmcluster). To access the cluster you login to the cluster headnode called _slurm_. Slurm accepts jobs, puts them in a queue until they can be executed, sends them to the compute nodes and manages the execution.

```
ssh slurm
```
Once on Slurm you see the same  home directory as  on _dover_ (or _foxcroft_). Go to the folder where you have placed your visibility project.  To submit a job on the command line use: 

```
hpcsub -N 1 -n 8 -cmd (your command here)
```

for example  (assume you have cd-ed to the directory where you have your project so that the executable _vis_ is in the current directory). 
```
hpcsub -N 1 -n 8 -cmd ./vis southport.asc  vis.asc  1000 1000 10 
```

_Datasets:_ The command above is assuming that you have the file _southport.asc_ in the current directory.    __AVOID copying data in your directory__. You can access a variety of grids from ```/mnt/research/gis/DEM/```. The Mt Rainier dataset is there, also Southport,  and many more;  ```ls /mnt/research/gis/DEM/``` to see the content.  Use the full path to the dataset in your command, for e.g.  ```/mnt/research/gis/DEM/southport.asc```. 

_Output files:_ Unless you specify otherwise, your code will generate the output files (visibility grid and bitmaps) in the current directory.  When you run with a large dataset, just comment out the line that saves the visibility grid (we don't need it for timing). Also comment out the line that saves the hillshade bitmap. The only output file of your code should be the viewshed-overlayed-on-the-hillshade bitmap. The bitmap can get quite large, so instead of saving it in the current directory (which may exceed your quota), use the following temporary space: ```/mnt/hpc/tmp/username/```.  In your code,  update the path where you save the bitmap (replace username with your username).

```
 save_pixel_buffer_to_file(&pb, "/mnt/hpc/tmp/username/map.viewshed-over-hillshade.bmp");
```
  
So to submit to the grid you would use something  like so: 

```
hpcsub -N 1 -n 8 -cmd ./vis /mnt/research/gis/DEM/mtrainier.asc  18900  21500 100 
```

The other way to submit a job to the grid is to  create a script (a text file) that contains the commands you want to run. This is  more convenient.   A sample script called _myscript.sh_ might look like this (you can create a new file with an editor like _nano_ or _vim_):
```
#!/bin/bash
#SBATCH --mail-type=BEGIN,END,FAIL

./vis /mnt/research/gis/DEM/mtrainier.asc  18900  21500 100
##you can put more commands here 
```
To run your job (with just one core), use : 
```
sbatch myscript.sh
```
Note that if you specify the command as _./vis_ ie in the current directory, you need to have the script in the  same directory as _./vis_.  

If your code has OpenMP  and  can use multiple cores, you can  request a number of cores: 
```
sbatch -N 1 -n (number-of-cpus-to-use) myscript.sh
```

From [HPC www](https://hpc.bowdoin.edu/hpcwiki/index.php?title=Linuxhelp:Slurmcluster): It is important to note that the program you are running must be able to support multiple CPUs, and you need to be able to tell it how many CPUs to use. If the program is only written to support a single CPU, it will not be able to use multiple CPUs simply by requesting them. The "-n (number of CPUs)" option simply tells the HPC Cluster that you are requesting that specific number of CPUs, and has no direct connection to the actual program that you are running.

Note that you also have to tell your program how many CPU cores to use, usually by passing an option to the program. To make it a little easier, you can replace the number of CPU cores in the submit script with "$SLURM_NPROCS". The $SLURM_NPROCS variable is the value of the number of cores that you asked for in the sbatch command.

For example, if your program uses the "-n" option to tell how many CPU cores (or threads) to use, you can do something like this in your submit script:
```
#!/bin/bash
#SBATCH --mail-type=BEGIN,END,FAIL

my-program-name -n $SLURM_NPROCS
```
If you submit this with "sbatch -N 1 -n 8 myscript.sh", the value of $SLURM_NPROCS will be 8. 

### Exclusive use of a machine 

Once you submit a job to the grid, the scheduler decides  what node to allocate to your job. There are different types of nodes on the grid, with slightly different specs.   If you request 8 cores, and the node has more cores available, the remaining ones will likely be allocated to a different job. For most purposes sharing is not a problem, but since we are timing  experiments other jobs competing to access the shared memory will affect the timings.  We want to request the same machine, and request it exclusively: 

```
sbatch -N 1 -n 8 --nodelist=moose30 --exclusive myscript.sh
```

This will always use the node called _moose30_ and will only allow jobs from your account to run.  

Note however that if you submit two jobs at the same time from your account, they will both run on the same node assuming there are resources available.  For example moose30 has 32 CPU cores, so if you submitted four jobs each needing 8 cpu cores, all four would run.  The --exclusive option prevents jobs from other accounts running on the node, but not multiple jobs from your account.

If two users send a job to _moose30_, the first job sent would run, and the job from the second user would wait in the queue, and would run once the first one finished.



When the job is done, you will see the output (what would have been printed on the screen) in the  directory from where you started the job, as ```slurm.oxxxx```, where xxxx is the ID number of the job. You will also get emails telling you  that the job was received, and when it was completed. 

## The Report

Summarize your work and findings in a brief report. Include the following: 

* Parallelization:
   * What parts of the code did you parallelize and how/why? Comment on what you tried and what worked/did not work. 
* Experimental evaluation 
   * Include a table showing for each dataset, the total running time with various number of threads: 1, 2, 3, 4, 8, 12 (in the table below T2 means the running time with 2 threads, etc). 

   | ---------------------|-|-|-|-|-|-|-|-|-|-| 
   | Dataset and viewpoint | T1 | T2| T3| T4 | T8| T12| 
   | set1 vp=(250,250,50) |    |    |   |    |   |    |   
   | southport vp=(1000,1000,10) |    |    |   |    |   |    |    
   | rainier vp=(19500,21000,100) |    |    |   |    |   |    |    
  

Also show the __speedup__ in each case, which is defined as T1/Tn, the ratio between the time to run with one thread and the time to run with _n_ threads. Ideally we would hope to see the speedup grow linearly with the number of threads, but of course that rarely happens in practice. 
* Findings: Comment on your findings.
* Bugs and extra features.
* Effort. Time you spent in: thinking, Programming; Testing; documenting; total.
* Reflection. Prompts: how challenging did you find this project? what did you learn by doing this project? What did you wish you did differently? If you worked as a team, how did that go? What would you like to explore further? — you don’t need to address all.

  

## What to turn in

* Check in your code to the github repository
* Message me the report.


