
## Lab 3 - Data models 


__Problem 1:__ Consider an area of 300km-by-300km to be represented as a grid at 
  *   100m resolution
  *   10m resolution
  *   1m resolution

The elevation values are represented as floating point numbers (4B). 
How many points and how much space (in GB) does the grid use, in each case? 


__Problem 2:__ Assume you have a TIN with n vertices stored as either the edge-based  or the vertex-based topological TIN structure.  Assume a vertex stores its coordinates (x,y,z) as floats (a float is 4B);  a  pointer is 8B.  What is teh space  used by the structure, as a function of  n? You can assume that e < 3n-6 and f < 2n -4. 


__Problem 3:__ We have an elevation grid for an area of  300km-by-300km at 1m resolution. The elevation values are represented as floating point numbers (4B). 

 * How much space does the grid use (in GB)?
 * Assume the grid undergoes a process of simplification, so that 90% of the grid points are eliminated, leaving 10% of the points.  These points are represented as a TIN with a topological edge-based structure.  How much space does the TIN use (in GB)? 
