---
layout: default 
title: Syllabus
nav_order: 1
---


# CSCI 3225: Algorithms for GIS, Fall 2023

- __Professor:__ Laura Toma (she/her)
- __Email:__ ltoma at bowdoin.edu
- __Office:__ Searles 219 
- __Class time:__  TR 2:50-4:15
- __Classroom:__  Searles 126


### Prerequisites 
csci 2200 (Algorithms) and csci 2330 (Systems). In other words, basic knowledge of:

-  Analysis (asymptotic notation, growth, recurrences); basic algorithms and data structures (searching, sorting, binary search trees, priority queues) and algorithm design techniques (divide-and-conquer, greedy)
- Programming in C/C++, Unix terminal commands and Makefiles



### Overview

Geographic Information Systems (GIS) is an umbrella term for systems
  that work with geographically referenced, or geospatial data such as
  maps and subdivisions, networks, points, surfaces and aerial
  imagery.  Routine tasks include visualizing multiple layers and
  types of data; performing surface analysis such as drainage and
  watersheds, visibility, gridding, interpolation and contouring;
  performing data exploration and spatial statistics; location
  analysis; and image processing of remote sensing imagery. GIS have
  long become indispensable tools in the earth, atmospheric and
  oceanographic sciences and their uses keep growing as more data is available. 

From a computer science perspective, GIS and geospatial data provide a
rich source of algorithmic problems. Furthermore these problems require efficient solutions
that work well in practice and scale up to large data.

This class explores algorithmic problems in GIS. Topics will be selected from: 
contour lines and contour trees, drainage, watersheds, flooding,
modeling sea level rise, visibility, line simplification and surface
simplification, LiDAR data, Delaunay traingulations, Voronoi diagrams,
B-trees, quadtrees, parallel processing with OpenMP, cache-efficient
algorithms, cache-efficient matrix multiplication and space-filling
curves. 

This is a projects course and will provide opportunities to design, implement and experiment with some of these
algorithms.


### Learning objectives

- Understand  fundamental problems encountered in GIS and their algorithmic solutions
- Gain experience designing algorithms that model GIS phenomena 
- Understand the importance of algorithm design, complexity analysis and generally the interplay between theory and practice
- Gain coding skills in C/C++  and work on a large project
- Improve communication and writing skills through presentations and project reports



### Syllabus overview

The list below is tentative and  very likely to change to fit our needs. 

- Data models (vector and raster, TINs, TIN representation)
- Contour lines
- Sea-level rise
- Drainage: aspect, slope, flow accumulation, watersheds, sink-watershed partition, flooding and pfastetter labeling
- Visibility: line-of-sight, viewshed, total viewshed
- Parallelization with OpenMP 
- Lidar data:  classification, lidar-to-raster and lidar-to-TIN
- Line simplification and surface simplification.
- Delaunay triangulation and Voronoi diagrams.
- Limitation of the RAM model and cache efficiency   
- Spatial indexing: B-trees,  Quadtrees and R-trees
- Spatial operations:  nearest neighbor and k-nearest neighbors
- Space-filling cuves: Z-order and Morton order


 
### Course requirements

The requirements for the class are attendance and active engagement during classes; and completion of 
assignments and projects.  Evaluation will be as follows: 

- __Class engagement: 20%__ 
- __Projects: 80%__

Note that __class engagement__ will significantly affect your grade.  Engagement does not mean having the right answers in class, but it means focusing and  working towards getting the right answers.  Examples of class engagement include attending class and consistently and independently engaging in discussions during class, volunteering ideas and making observations, asking questions, working on the whiteboard, coming up with algorithms, drawng diagrams, talking to your peers about the material, and generally maintaining a focus on the class.


**Communication:** Class communication will be entirely on Slack. You will need to monitor Slack for class updates, including possible
  cancellations or delays. The code to join Slack is posted on Canvas.

     

