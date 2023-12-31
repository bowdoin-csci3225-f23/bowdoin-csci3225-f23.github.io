---
layout: default 
title: --Lab (OpenMP)
nav_order: 29
---


## Lab: Programming with OpenMP 


We'll use the OpenMP  tutorial by Tim Mattson from Intel, available on YouTube [here](https://www.youtube.com/watch?v=6jFkNjhJ-Z4&list=PLLX-Q6B8xqZ8n8bwjGdzBJ25X2utwnoEG&index=3). You'll need to go through the first 10 videos (modules 1 through 5)

*  01: intro (4.5 min)
*  02: part 1 (8 min) Module 1
*  02: part 2 (7 min) Module 1
*  03: Module 2 (5 min) compiling
*  04: Discussion 1 (10 min)
*  05: Module 3 (11.5 min) creating threads
*  06: Discussion 2 (11 min) the simple PI program in parallel
*  07: Module 4 (8 min) synchronization
*  08: Discussion 3 (5 min) overhead and eliminating false sharing
*  09: part 1 Module 5 (10 min) parallel loops
*  09: part 2 Module 5 (7 min) parallel loops
* 10: Discussion 4 (6.5 min) parallel PI program wrap -up 

Other useful materials:

  * Slides: [A hands-on intro to OpenMP](https://github.com/tgmattso/OpenMP_intro_tutorial)  by Tim Mattson, Intel
  *  A 1.5 hour [talk](https://www.youtube.com/watch?v=fn2VAUSw6cI), called "Intro to SMP programming with OpenMP" also by Tim Mattson 


***

### OpenMP in a nutshell


OpenMP is a library for parallel programming in the SMP (symmetric multi-processors, or shared-memory processors) model. When programming with OpenMP, all threads share memory and data. OpenMP supports C, C++ and Fortran. The OpenMP functions are included in a header file called ```omp.h```.

Program structure: An OpenMP program has sections that are sequential and sections that are parallel. In general an OpenMP program starts with a sequential section in which it sets up the environment, initializes the variables, and so on.

When run, an OpenMP program will use one thread (in the sequential sections), and several threads (in the parallel sections).

There is one thread that runs from the beginning to the end, and it's called the master thread. The parallel sections of the program will cause additional threads to fork. These are called the slave threads.

A section of code that is to be executed in parallel is marked by a special directive ```omp pragma```. When the execution reaches a parallel section (marked by omp pragma), this directive will cause slave threads to form. Each thread executes the parallel section of the code independently. When a thread finishes, it joins the master. When all threads finish, the master continues with code following the parallel section.

Each thread has an ID attached to it that can be obtained using a runtime library function (called```omp_get_thread_num()```). The ID of the master thread is 0.

When specifying a parallel region, we'll also: 
* specify whether the variables in the parallel section are private or shared
* specify how/if the threads are synchronized
* specify how to parallelize loops
* specify how the works is divided between threads (scheduling) 

Overall the advantage of OpenMP is a good trade-off between simplicity of use and efficiency: it  hides the low-level details and allows the programmer to describe the parallel code with high-level constructs. There are other, more efficient and more general parallel libraries and lower-level parallel code is possible (e.g. OpenMPI). 



### Hello world with OpenMP threads

The OpenMP functions are included in a header file called ```omp.h```, which you need to include at the top of your code. 
 
OpenMP lets you specify which region of your code you want to run in parallel. 
To specify a region that is to be run in parallel you use a pragma block:  The basic directive is:

```
#pragma omp parallel 
{

}
```

When the master thread reaches this line, it forks additional threads to carry out the work enclosed in the block following the #pragma construct. The block is executed by all threads in parallel. The original thread will be denoted as master thread with thread-id 0.


_hellosmp.c:_  We'll have multiple threads write  "Hello, world" in parallel.  Let's create the following file, and save it as ```hellosmp.c```.  

```
#include < stdio.h >
#include <omp.h>

int main(void)
{
    #pragma omp parallel
    {
    printf("Hello, world.\n");
    }

  return 0;
}
```


### Compiling and running OpenMP code

The public linux machines dover and foxcroft have gcc/g++ installed with OpenMP support. All you need to do is use the _-fopenmp_ flag on the command line:

```gcc -fopenmp hellosmp.c  -o  hellosmp```

OpenMP on a Mac:  When you type _gcc_ this is linked to _clang_, the native apple compiler, which  is installed without _openmp_ support.  To test, go to the terminal and try to compile something:

```gcc -fopenmp hellosmp.c  -o  hellosmp```

If you get an error message like so, that means your compiler does not have openMP support.

```
hellosmp.c:12:10: fatal error: 'omp.h' file not found
#include 
         ^
1 error generated.
make: *** [hellosmp.o] Error 1
```

Here's what I did:

* I installed [Homebrew](http://brew.sh/index.html)

```/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"```

* Then I asked brew to install gcc:

``` brew install gcc```

* Then type ```gcc``` and press tab; it will complete with all the versions of gcc installed:

```
(base) ltoma@XVR66RXWMT openMP % gcc
gcc            gcc-13         gcc-ar-13      gcc-nm-13      gcc-ranlib-13
```

* The guess here is that gcc-13 is the latest version, so I use it to compile:

```gcc-13  -fopenmp hellosmp.c -o hellosmp```

Works!


Since we don't specify how many threads we want, we get OpenMP's default value (which seems to be  the number of cores on the machine).  On dover, I got 24 hellos, for 24 threads. On my desktop I get (only) 8. How many do you get?


```
Hello, world.
Hello, world.
Hello, world.
Hello, world.
Hello, world.
Hello, world.
Hello, world.
Hello, world.
```

Note that the threads are all writing to the standard output, and there is a race to share it. The way the threads are interleaved is completely arbitrary, and you can get garbled output:

```
Hello, wHello, woorld.
rld.
```

Understanding race conditions, how they can happen and how to avoid them, is at the core of parallel programming.  
