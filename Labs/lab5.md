---
layout: default 
title: --Lab 5(SLR)
nav_order: 25
---

# Lab 5: Sea level rise 


Come up with an algorithm to  model what parts of a terrain are flooded when the sea-level rises with an arbitrary amount _x_.  

The inputs to the algorithm are a grid DEM (arcascii order), and a sea-level rise _x_. For example, calling your code with : 

> ./slr brunswick.asc 3

will create another grid, say _flooded.asc_, in which every point will be either 1 (if flooded), or 0 (if not flooded). 

