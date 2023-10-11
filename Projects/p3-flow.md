---
layout: default 
title: --Project 3 (Flow)
nav_order: 11
---

## Project 3:  Computing Flow on Terrains  

(editing in progress) 

*** 
* __Assigned:__ Thursday, October 12
* __Due:__ Thursday October 26th 
* Group policy: Partner-optional

The goal of this project is flow on terrains. You will create a
program to compute and visualize the flow network derived from flow
direction and flow accumulation and write a report to showcase your
work.


__Input__: an elevation grid

__Output:__ a flow direction grid, a flow accumulation grid  and a number of bitmaps. 


Your code will read on the command line the name of an elevation grid,
and also the name of the flow direction grid and flow accumulation
grid which are to be created. For example, running with

```
./flow -e ~/DEMs/set1.asc -d fd.asc -a fa.asc
```

or
```
./flow ~/DEMs/set1.asc fd.asc fa.asc
```

will read _~/DEMs/set1.as_ as the elevation grid and will create a flow direction and
flow accumulation grid in the current directory called _fd.asc_ and
_fa.asc_ respectively (all grids in arcascii format).  

You will create the following bitmaps:

* elevation overlayed on hillshade
* flow direction (without handling flat areas), grayscale 
* flow accumulation (without flat areas), color intervals
* flow direction (with plateaus but no flooding), grayscale
* flow accumulation (with plateaus but no flooding), color interval
* same as above, overlayed on hillshade
* flow accumulation (with flooding and plateaus), color interval
* same as above, overlayed on hillshade



### Datasets


You can use the same dataset as in the previous project. The
flow network will look better on a mountainous terrain, but the effects of handling plateaus and flooding wil be larger on flat terrains.

_Test datasets:_ You can download the datasets used to generate the
maps on this page [here](https://tildesites.bowdoin.edu/~ltoma/teaching/DEM/). 


### Overview


You will start by reading the elevation grid and printing basic information about it (_grid.c_ has a function to do this). While you
are at it,  create a hillshade grid, a pixel_buffer, and use them to create a bitmap of the elevation overlayed on the hillshade.

Then you'll want to create the flow direction and flow accumulation grids. The module _grid.c_ has a function to create/initialize a grid based on on another grid, for e.g. 
```Grid * fd_grid = grid_init_from(elev_grid);```
 
In order to see the differences, you will compute  the flow direction (FD) in three, increasingly better variants: 

1. The simplest version of FD assigns d8 flow direction to all points that have at least one lower neighbor; all points that do not have a lower neighbor are assigned a special "flat" direction (for e.g. you could define FLAT_DIR to be -1) 

1. The second version of FD is the same as above, but it also assigns flow  direction to flat areas that have outlet points (the plateaus). The idea here is to traverse the plateau from the outlet points and direct points on the plateau  towards the the outlets. 

1. The third version of FD  first floods the terrain to eliminate the pits and then assigns FD on the flooded terrain as in (2) above.  A flooded terrain does not have pits and all the flat areas will have outlet points, so  this approach will assign FD to all points. 

For each variant, you will use the resulting FD grid to compute the corresponding flow accumulation (FA) grid.  You'll encapsulate computing the FD and FA grids in two functions and below are the names and parameters suggested (you can use these as they are, or feel free to adjust to match your style):

```

/*
  elev_grid: input elevation grid
  fd_grid:   output flowdir grid, initialized
  populates fd_grid with d8 flow direction values; all points with no lower neighbors are assigned FLAT_DIR 
*/
void compute_d8_grid_skipflat(const Grid* elev_grid, Grid* fd_grid);

/*
  elev_grid: input elevation grid
  fd_grid:   output FD grid, initialized
  populates fd_grid with d8 flow direction values.
  FD of points on flat areas that have outlets is set towards the outlets.
*/
void compute_d8_grid_withplateaus(const Grid* elev_grid, Grid* fd_grid);

/*
  elev_grid: input elevation grid
  fd_grid:   input FD grid
  fa_grid:   output FA grid, assume initialized
  distribute flow according to fd_grid and populate fa_grid with FA values 
*/
void compute_fa_grid(const Grid* elev_grid, const Grid* fd_grid, Grid* fa_grid);

```  

### The main() function 

In your main() function you will call the three functions to compute FD as above, and for each FD grid computed you will call the function to compute a FA grid corresponding to this FD grid, and then create the bitmaps you need.  If you encapsulated your code in functions as above, the main() function will look nice and easy: 


```

  printf("\n\nFD AND FA SKIPPING FLAT AREAS\n");

  compute_d8_grid_skipflat(elev_grid,  fd_grid);
  //create grayscale bitmap 
  //save_pixel_buffer_to_file(&pb, "map.fd-skipflat.bmp");

  compute_fa_grid(elev_grid, fd_grid, fa_grid);
  //create bitmaps "map.fa-skipflat.bmp" and  "map.fa_over-hillshade.bmp"

  printf("\n\nFD AND FA WITH PLATEAUS BUT NO FLOODING\n");

  compute_d8_grid_withplateaus(elev_grid,  fd_grid);
  //create grayscale bitmap
  //save_pixel_buffer_to_file(&pb, "map.fd-withplateaus.bmp");

  compute_fa_grid(elev_grid, fd_grid, fa_grid);
  //create bitmaps  "map.fa-withplateaus.bmp" and "map.fa-withplateaus-over-hillshade.bmp"

  printf("\n\nFD AND FA WITH FLOODING AND PLATEAUS\n");

  //flood sinks and create a flooded grid
  compute_d8_grid_withplateaus(flooded_grid,  fd_grid);

  compute_fa_grid(flooded_grid, fd_grid, fa_grid);
  //create bitmaps "map.fa-flooded.bmp" and "map.fa-flooded-over-hillshade.bmp"

```

At some point along the way or at the end, you'll want to save the FD
and FA grids to disk (_grid.c_ has a function to do that).

And finally, once the grids and the bitmaps written to disk, you
need to free the memory for the grids and the pixel buffers.




### Details on d8 flow direction


Computing a D8-flow direction for a point is encapsulated in couple of functions: 

```
/*
  elev_grid: input elevation grid
  r, c: row and column of a point in the grid 
  return: the d8 flow direction of (r,c)
  - Points on the edge of the grid  flow outside.
  - Points with elev=nodata, return  nodata
  - Points  with all neighbors > elev[r,c] return  PIT_DIR 
  - Points with all neighbors  at same elevation as elev[r,c] return FLAT_DIR
    (nodata neighbors are ignored in these calculations)
  
  d8 dir convention:
  32 64 128
  16  x  1
  8   4  2
*/
int d8(const Grid* elev_grid, int r, int c);
```

where 
```
#define FLAT_DIR -1
#define PIT_DIR  -2
```

And the second function  will be convenient when accumulating flow:

```
/* fd_grid: input fd grid
   (a,b) and (c,d) are points in the grid 
   return 1 if (a, b) is a neighbor of (c, d) and (a,b) flowdir points to (c, d)
   return 0 otherwise 

   d8 dir convention: 
   32 64 128
   16  x  1
   8   4  2
*/
int flows_into(const Grid* fd_grid, int a, int b, int c, int d);
```





### Visualizing flow direction and accumulation

You need to create a couple  of bitmaps:

* FD grayscale
* FA color interval
* FA overlayed on hillshade

You can use the function that creates a grayscale pixel buffer from a
grid, which you wrote in the previous project.  I wanted to see the
flat areas clearly, so I wrote a new function to create a (grayscale)
pixel buffer from a FD grid, which sets the points which are FLAT_DIR
or PIT_DIR in red.  This allows to see clearly the flat areas in the
FD map. 

```
grid_flowdir_to_pixelbuffer(const Grid* fd_grid, PixelBuffer* pb);
```

For the flow accumulation, you want to create a color interval
bitmap. For a start, you can use the function that createts a color
interval bitmap which you wrote for the first project. But it won't
look good, because the values in the FA grid are not uniformly
distributed, but very skewed towards low values.  You will need to
write a new function to create a pixel bufefr from a FA grid, which
sets the values for the intervals in such a way to be able to see the
ridges and the river network. This will take some experimenting, and
depends on the terrain to some extent.

```
void grid_flowaccu_to_pixelbuffer(const Grid* flow_grid, PixelBuffer* pb);
```

 A good  point to start  is to use the maximum value in the FA grid  (_fa_grid->max_value_).  Points with values above say c * _log(maxfa)_ or so are small rivers --- find what value of _c_ looks good.  Then choose a larger value (perhaps twice that) for th enext interval.

The maps below use  4 intervals to show flow accumulation, with the first one being the ridges, the second being the areas with very low flow (most of teh terrain), teh third one being small rivers, and the fourth one being larger rivers.




### Structure of the code


Good coding practices recommend breaking the code into files that encapsulate parts of the functionality, similar to how you would break your code into classes (remember Data Structures projects).

These are the files that were given with last project, which you’ll use again:

    stb_image_write.h
    grid.h, grid.c
    pixel_buffer.h, pixel_buffer.c

From the previous project you'll use:
    map.h, map.c

You'll create the following additional files:


    drainage.hpp, drainage.cpp
    flowmain.c


map.{h,c}: All functions to create bitmaps from grids. Need to include grid.h, pixel_buffer.h. Add your functions to create bitmaps for FD and FA.


drainage.{hpp, cpp}: Functions to compute the FD and FA. Include grid.h

flowmain.c: The main() function is here. Read parameters from user, create grids, compute the FD and FA grids  and create the bitmaps. Include grid.h, map.h, drainage.hpp

Note that the drainage files need to use a priority queue, and we'll use a c++ structure. Currently the compiler allows to mix c and c++ code in a project so we'll do that (although you’ll get a warning).

Once you accept the code on github classroom, a good place to start is
by copying all code from project2 over into project3, except slrmain().
Then create a file flowmain.c where you add a main function
— here you will eventually call the functions to create FD and FA grids and visualize the
flooding. As you get things to compile, start with an empty main
function, and then gradually add code to open a grid, create maps and
so on.

You will also need to modify the Makefile from the previous project.



### Working with a priority queue in C++

[to do]


### The Report

You will write a report showcasing your work. Include:

    1. The dataset you used, location, number of rows and columns, resolution and provenance.
    1. Results
        * run your code with southport.asc
        * run your code with set1.asc
        * run your code with YOUR_DATASET.asc
    1. Maps: for each dataset create the following maps:
     * color gradient over hillshade
     * the simplest FD (that does not handle flat areas) grayscale
     * three FA maps, each one as a color gradient and also overlayed on hillshade (total 6 maps)
     Total 8 maps for each dataset 
    
    Note that if the bitmaps are large  you should NOT add them to the repository.  Just make a note and connect with me so that I can get them from you another way (a link to drop box for e.g.). 
            

    1. Bugs and extra features.

    1. Effort

    Time you spent in: thinking, Programming; Testing; documenting; total.
    Reflection

 Prompts: how challenging did you find this project? what did you learn by doing this project? What did you wish you did differently? If you worked as a team, how did that go? What would you like to explore further? — you don’t need to address all.



### What to turn in

    Check in your code to the github repository
    Message me the report.

### Final remarks

There are many small steps to this project, and starting early and
making progress every day is important so that you learning is
rewarding and enjoyable. Coding projects can quickly become
overwhelming if put off too long. Think one piece at a time, and
remember this is an opportunity to learn.

A 3000-level class means you work towards being independent and
debugging your code independently. Use the internet as much as you
can, to answer your questions on how to do things in C/C++, syntax and
so on. Write code incrementally, in small pieces at a time, so that
you always know where the error is coming from. Use the bitmaps and
prints to know what you are computing at all times.

Put differently, if you don't approach coding incrementally, you won't
be able to debug your code.

Enjoy!
