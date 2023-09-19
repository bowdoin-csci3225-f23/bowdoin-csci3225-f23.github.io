---
layout: default 
title: --Lab 6 (SLR)
nav_order: 25
---

# Lab 6: More SLR 


1. Wrap up your ideas from last time and write detailed pseudocode for SLR-flooding a terrain. 

The inputs to the algorithm are a grid DEM, and an arbitrary sea-level rise _x_.  The output is a grid of the same size and extent,  in which every point will be set as  1 if it is was initially land and will flooded  when the sea level rises by _x_ feet; and 0 otherwise. 

Note that with this definition of output, a point that is sea is not considered flooded (because it is sea), so it will be marked as 0. Only land points that are under water are considered flooded. This is just one way to define the output. 

2. There are two ways to model the flooding above: recursive, and with a queue. Whichever one you came up with, consider the other one.  The approach with a queue is perhaps more intuitive because it resembles BFS. In fact, it __is__ BFS. The typical BFS starts from one point, this one starts from all points on the boundary that are sea. This is called a _multi-source BFS_.

3. Consider a more general problems:
   (a) Given a point _p = (i, j)_ in the grid, what is the minimum rise _x_ which floods _p_?
   (b) Do this for the entire grid:  for every point _p=(i, j)_ in the grid, what is the level at which _p_ floods?

   Hint: Generalize the BFS from above, and express this as a shortest-path problem. 
