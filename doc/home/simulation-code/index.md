# R-Vis Simulation Code

R-Vis supports use of simulations coded in a single .R file. Code must
conform to one of two layout types:

* [Executive Function](#executive-function)
  
  In your code, a unary function is defined that runs the simulation. The function's single argument is a list of parameter values, and it returns
  the results in a single object (list or matrix or data frame).

* [Template](#template)

  Your code is a listing that contains commands to be executed sequentially, top to
  bottom. Parameter assignments and acquisition of outputs occur in the global
  environment.

## Executive Function

This is the preferred layout for use with R-Vis as it the more efficient of the two. At the start of each session, your code is sourced just once.
After that, when R-Vis needs to re-run the simulation, it will find and invoke the
executive function.

When importing your code into R-Vis, in addition to specifying which function is
the executive, you also specify which object (list or numerical vector) present in the global
environment should be the argument to the executive function. The argument is a
collection of inputs, typically parameters assignments. Those elements of the collection
containing scalars (numerical vectors of length one) are presented in the RVis
user interface, and are the targets of R-Vis modules such as Morris and
MCMC.

### Executive Function - Input Collection

In your code, you might define the inputs like this:

``` R
a <- 1
b <- 2
params <- list(a = a, b = b)
```

**Note:** the elements in the list must be tagged (a = ...). R-Vis relies on symbol
names to manipulate the objects that your code creates.

### Executive Function - Function Definition

In your code, define the executive like this:

``` R
exec <- function(params) {

  # any preprocessing needed, e.g. compute time sequence

  # execute simulation, e.g. call deSolve

  # any postprocessing

  # return outputs

}
```

### Executive Function - Output

The output returned by the executive function is a single list, matrix, or
data frame. If a list, then each element must be a numerical vector, and
all elements must be the same length. In all cases, items must be named:
list elements have tags; matrices and data frames have column names.

The first element (if a list), or the first column (if a matrix or data frame),
is the independent variable (time, for instance). Successive columns contain data for the dependent variables.

The output will look something like this:

|Time (s)|X [mg/l]|Y [mg/l]|Z [mg/l]|
|:------:|-------:|-------:|-------:|
|0       |       0|       0|       0|
|1       |     0.1|     0.2|     0.3|
|...     |     ...|     ...|     ...|

### Examples

[This is a skeleton script](https://github.com/r-vis/Simulations/blob/master/exec/Skeleton.R)
that can serve as a starting point for your own code.

[This is some sample code](https://github.com/r-vis/Simulations/blob/master/exec/Lorenz.R)
adapted from the deSolve manual and reformatted to use an executive function.

## Template

If your code is a list of commands, R-Vis will source your script file each
time it needs to execute the simulation. The commands are executed from top to bottom, typically with parameter assignments occurring near the top of the script, with simulation logic next, and concluding
with outputs computed and assigned to one or more symbol names. When R-Vis needs to vary one or more of the parameter values, its will substitute those values into the source code and rewrite the file before sourcing your script file.

R-Vis will detect only those parameter assignments made in the global environment.
A parameter assignment is a numerical vector of length one.

Similarly, R-Vis will detect only outputs present in the global environment. An
output can be one of the following:

* Numerical vector
* Numerical matrix (with column names specified)
* List of numerical vectors (with each vector tagged)
* Data frame

Combined, the tags and column names must be unique. Columns and vectors must be of equal length.

[This is some sample code](https://github.com/r-vis/Simulations/blob/master/tmpl/ff_sed_oxy.R)
from the deSolve manual, reformatted as a listing suitable for use by R-Vis.

## Note

Ensure the following:

* Your code uses the R assignment operator, ```<-```, not equals (```=```), for assignments in the global environment.

* Your comments for a line of code consist of a single ```#``` only. Format your
  comments with a brief description on the line above, and the unit trailing the
  assignment or formula:

  ``` R
  # Quantity for such and such...
  Qx <- 123 # mg/l
  ```

* Code that R-Vis is expected to recognise and manipulate, e.g. parameter
  assignments, resides on a line of its own. Don't do this:

  ```Qx <- 123; Qy <- 456```
