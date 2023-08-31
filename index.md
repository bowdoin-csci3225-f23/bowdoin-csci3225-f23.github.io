---
layout: default 
title: Syllabus
nav_order: 1
---


## CSCI 3225: Algorithms for GIS, Fall 2023

## Syllabus


__Instructor:__ Laura Toma, email: ltoma at bowdoin.edu, office: Searles 219 

__Class time:__  TR 2:50-4:15

__Classroom:__  Searles 126

**Prerequisites:** csci 2200 (Algorithms) and csci 2330 (Systems). In other words, basic knowledge of:

-  analysis (asymptotic notation, growth, recurrences)
-  basic algorithms and data structures (searching, sorting, binary search trees, priority queues) and algorithm design techniques (divide-and-conquer, greedy)
- programming in C/C++
- Unix terminal commands and Makefiles


**Overview:**




Geographic Information Systems (GIS) is an umbrella term for systems
  that work with geographically referenced, or geospatial data such as
  maps and subdivisions, networks, points, surfaces and aerial
  imagery.  Routine tasks include visualizing multiple layers and
  types of data; performing surface analysis such as drainage and
  watersheds, visibility, gridding, interpolation and contouring;
  performing data exploration and spatial statistics; location
  analysis; image processing and analysis of remote sensing imagery,
  and more. GIS have long become indispensable tools in earth,
  atmospheric and oceanographic sciences where they are used to track
  wildfires and tornadoes, model areas susceptible to flooding or
  erosion, monitor landscape change, and many more. 

From a computer science perspective, GIS and geospatial data provide a
rich source of algorithmic problems and require efficient solutions
that work well in practice and scale up to large data.

This class explores algorithmic problems in GIS. Topics include
contour lines and contour trees, drainage, watersheds, flooding,
modeling sea level rise, visibility, line simplification and surface
simplification, LiDAR data, Delaunay traingulations, Voronoi diagrams,
B-trees, quadtrees, parallel processing with OpenMP, cache-efficient
algorithms, cache-efficient matrix multiplication and space-filling
curves. The class will provide opportunities to implement and experiment with
some of these algorithms.


This is a projects course.  The work for the class comes from
engagement and programming projects.




**Learning goals:** 

- Engage with some of the problems encountered in GIS and understand
  the algorithms and techniques used in their solutions

- Engage with more complex, advanced algorithms

- Experience the importance of algorithm complexity analysis and the
  interplay between theory and practice

- Improve coding skills in C/C++  and work on a large project

- Work on communication skills: give a presentation and write a paper 





**Syllabus overview:**


The tentative syllabus:

- Data models (vector and raster; TINs; TIN representation)
- Contour lines
-  Modeling sea-level rise
- Drainage: aspect, slope, flow accumulation, watersheds, sink-watershed partition, flooding and pfastetter labeling
- Visibility: line-of-sight, viewshed, total viewshed
- Parallelization with OpenMP 
- Lidar data; classification; lidar-to-raster and lidar-to-TIN
- Line simplification and surface simplification.
- Delaunay triangulation and Voronoi diagrams. 
- Spatial indexing: B-trees,  Quadtrees and R-trees
- Spatial operations:  nearest neighbor and k-nearest neighbors
- Space-filling cuves: Z-order and Morton order


 
**Textbooks:** [none]

**Communication:** Class communication will be entirely on Slack. You
  will need to monitor Slack for class updates, including possible
  cancellations or delays. The code to join Slack is posted on Canvas.

     
 
**Work and Grading Policy:** The work for the class consists of
  class engagement and programming projects.

- __Class engagement: 20%__ This means attending class,  working with
  your group, consistently asking questions, engaging in discussions, volunteering
  answers, participating on Slack, attending office hours.


- __Projects: 80%__



**Time Commitment:**
This is a 3000-level CS class and will demand a significant time commitment. It is critical that you budget your time accordingly.  

