
# --Lab 3: Grid or TIN?


__Problem 1:__ Consider an area of 300km-by-300km to be represented as a grid at 
  *   100m resolution
  *   10m resolution
  *   1m resolution

The elevation values are represented as floating point numbers (a float is 4B). 
How many points and how much space (in GB) does the grid use, in each case? 


__Problem 2:__ Assume you have a TIN with n vertices stored as a topological structure (edge-based  or  triangle-based).  Assume a vertex stores its coordinates (x,y,z) as floats (a float is 4B) and a  pointer is 8B.  What is the space  used by the structure, as a function of  n? 


Note: Euler formula tells us that _v-e+f = 2_. Furthermore if all faces are triangles, the total number of edges in all triangles is 3f, and since every edge is adjacent to two  faces we would have counted every edge twice. So we get that _3f=2e_, and from here: 

Proposition: On a triangular mesh in which the utside face is also triangulated, we have that _e = 3n-6_ and _f = 2n -4_. 

In general if the outside face is not triangulated,  we'll have that e < 3n-6 and f < 2n -4.  Use these relations to derive a bound  for the space used by the TIN structure as a function of _n_. 

Based on  your answer, which structure is more space-efficient? 


__Problem 3:__ We have an elevation grid for an area of  300km-by-300km at 1m resolution. The elevation values are represented as floating point numbers (4B). 

 * How much space does the grid use (in GB)?
 * Assume the grid undergoes a process of simplification, so that 90% of the grid points are eliminated, leaving 10% of the points.  These points are represented as a TIN with a topological triangle-based structure.  How much space does the TIN use (in GB)? 
