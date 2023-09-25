---
layout: default 
title: --Lab 6 (SLR)
nav_order: 26
---

# Lab 6: More SLR 


__Problem 1:__ Wrap up your ideas from last time and write detailed pseudocode for SLR-flooding a terrain. 

The inputs to the algorithm are a grid DEM, and an arbitrary sea-level rise _x_.  The output is a grid of the same size and extent,  in which every point will be set as: 
* 1 if it was initially land and floods  when the sea level rises by _x_ feet;
* 0 otherwise. 

Note that the way we defined the output: Only land points that get under water are considered flooded . 
Points that are initially sea are not considered flooded so they are  marked as 0. 

__Problem 2:__ There are two ways to model the flooding above: recursive, and with a queue. Whichever one you came up with, consider the other one.  The approach with a queue is perhaps more intuitive because it resembles BFS. In fact, it __is__ BFS. The typical BFS starts from one point, this one starts from all points on the boundary that are sea (called a _multi-source BFS_).

__Problem 3:__ Consider a more general problems:
 
 * Given a point _p = (i, j)_ in the grid, what is the minimum rise _x_ which floods _p_?
 * Do this for the entire grid:   find the level at which _p_ floods,  for __all__  point _p=(i, j)_ in the grid

Hint: Generalize the BFS from above, and express this as a shortest-path problem. A path on a grid consists of cells. 
   * Consider a path between the sea and a cell _p_. Imagine the water coming to _p_ along this path, and flooding this path until reaching _p_. Which cell along this path tells us  at what level will _p_  flood?
   * Use this to define the cost of a path
   * Of all paths from p to the sea, which one do we want?  
