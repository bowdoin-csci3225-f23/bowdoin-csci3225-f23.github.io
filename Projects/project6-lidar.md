---
layout: default 
title: --Project 6 (Lidar)
nav_order: 14
---

## Project 6: Classifying Lidar data   


*** 
* __Due:__ by Saturday  December 16th, 1pm
* Group policy: Partner-optional

***

### Dataset ```france.las```

```
./main data/france.txt      
read total 101206 points
	bounding box:  x=[876734.00, 876834.00], y=[2260797.00,2260897.00], z=[348.28,362.93]
lidar_to_dsm:
	n=101206, sqrt(n) =318, corresponding cell_x = 0.3, cell_y=0.314
	starting with: nrows = 319,  ncols =319, cellsize = 0.314
	nb.last returns: 92755 (out of 101206 total points)
	#points/cell     #cells
	         0      37843
	         1      41018
	         2      18167
	         3       4006
	         4        535
	         5         93
	         6         34
	         7         20
	         8         16
	         9          9
	        10          8
	        11          3
	        12          6
	        13          1
	        14          1
	        15          1
	     total      92755
	       avg       0.91 (nb.points/cells)
	       avg       1.45 (nb.points/non-empty cells)
	grid size accepted.
lidar_to_dsm: done
grid dsm (0x600000658000):
	n=101761 [rows=319,cols=319], range=[348.28, 362.93], avg value=352.9 nodata=37843 (37.2%)
writing map.dsm-grayscale.bmp

```

![](p6-fr1.png)
![](p6-fr2.png)
![](p6-fr3.png)
![](p6-fr4.png)
![](p6-fr5.png)

****
