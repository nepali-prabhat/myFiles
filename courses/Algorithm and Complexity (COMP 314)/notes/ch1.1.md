## Mathematical preliminaries of foundations

### 1. Growth of functions

The amount of resource an algorithm needs as the data size increases.

* 1.1 Constant growth rate `O(1)`
	
    The resource doesn't change. 
* 1.2 Logarithmic growth rate `O(log(n))`

	The resource needs to grow one unit each time the data is doubled.
* 1.3 Linear growth rate `O(n)`

	The resource needs to grow in proportioinal to data size. 
* 1.4 Log linear growth rate

	Slightly curved line. `O(nlog(n))`
* 1.5 Quardatic growth rate `O(n^2)`
* 1.6 Cubic growth rate `O(n^3)`
* 1.7 Exponential growth rate `O(e^n)` `O(2^n)` `O(n!)`

### 2. Asymptotic Analysis
How algorithm's running time scales with the size of input.

* Theta notation (upper and lower) 
 
	$0\leq c_{1}g(n)\leq f(n)\leq c_{2}g(n)$
* Big O (upper)
    
  $0\leq f(n)\leq cg(n)$
* Omega (lower)

  $0\leq cg(n) \leq f(n)$

  
