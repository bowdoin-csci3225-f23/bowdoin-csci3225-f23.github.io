---
layout: default 
title: --Project 4 (Vis)
nav_order: 12
---

[IN PROGRESS] 

## Project 4:  Visibility on Terrains  


*** 
* __Assigned:__ Friday, October 27
* __Due:__ Tuesday  October 31st 
* Group policy: Partner-optional

***

Have you ever wondered what would be the view from the top of  Mt. Rainier?  In this project you will (partially) answer this question, with lots of sweat and hard work, but without the climb:   You will write code to compute the viewshed of an arbitrary  point on a terrain, download high resolution data for Mt. Rainier from OpenTopo, and use your code to compute viewsheds  of points that fall approximately on the top. 

__Input__: an elevation grid, the coordinates (r,c) of the viewpoint, the elevation of the viewpoint above the terrain

__Output:__ a viewshed grid (in arcascii format)  and a bitmap of the viewshed overlayed onto the elevation hillshade.  Your code  will also print the size of the viewshed (nb. of points that are visible). 


### The interface 

Your code will read on the command line the name of an elevation grid, the row and column coordinates of the viewpoint,  its elevation above ground , and the  name of the viewshed grid which is to be created. For example, running with

```
./vis -e ~/DEMs/rainier.asc -v  vis.asc  -r 1000 -c 1000 -h 10 
```

or
```
./flow ~/DEMs/rainier.asc vis.asc 1000 1000 10 
```


