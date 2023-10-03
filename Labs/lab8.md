---
layout: default 
title: --Lab 8 (Flat areas)
nav_order: 28
---

# Lab 8: Handling flat areas 


__Problem 1 [Plateaus]:__ Come up with an algorithm to assign flow direction on plateaus, which we defined as flat areas that have at least one point on their boundary that has flow direction (the outlet for the plateau).  We want to assign FD on the plateau  so that the water is routed to the closest outlet point  on the boundary of the plateau.  The input is an elevation grid, and the output is the FD grid. 

Start by thinking how to do this for one specific plateau.  Then think how you can extend this to the whole grid, and process all plteaus all at once. 
 

__Problem 2 [sink watershed]:__ Assume you are given a point _p=(i,j)_ that is on  the boundary of a sink (ie it has FD=0, and all its neighbors are either FD=0 or have elev > p). Describe an algorithm that finds the __watershed of p__, which is the set of all points in the grid that flow through _p_. 


__Problem 3 [Sink watershed partition:]__  Given an elevation grid, come up with an algorithm that creates a new grid, _watershed[]_ in which every point _p=(i,j)_ is labeled with the sink that it flows to. Points that flow off the bounday of the grid are considered to be part of the "sea" watershed. 

