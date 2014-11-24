ePICURE
======================================================================

Python Isogeometric CUrve REconstruction
----------------------------------------------------------------------

This is the final project for the course **Applied Mathematics: an
Introduction to Numerical Analysis and Sientific Computing**. It
consists of several small sub-projects, that will combine together in
an open source software for curve resconstruction, analysis and
simulation. We will apply this to micro-swimming problems, but several
other applications are possible...

The students should be familiar with

- Interpolation
- Integration
- Direct and iterative solution of linear systems
- Solution of non-linear systems
- Systems of Ordinary Differential Equations (ODEs)
- Finite difference and Galerkin methods

Objects
------------------------------------------------------------

At each time frame, an object can be described by

- `m` numbers (maximum 6), maximum three for the *location*, and
   maximum three for the *rotation*, describing the **global
   position** (location and rotation) of the object
   
- a collection of `n` numbers, describing the **local shape** of the
   object. Examples are:

	- a collection of points, describing a curve
	- a collection of points, describing a mesh
	- a collection *local locations and rotations*, describing
       assemblies of rigid bodies
	- *use your imagination!*

The full time dependent evolution of an object (a *stroke*) is
described by a curve in time **for each of the numbers above** (a
**path**). The time curves are **polynomial curves**, or **NURBS
curves**. In any case, they can be described by *coefficients* times
*basis functions*.

Components
------------------------------------------------------------

Each component should have a *well documented interface*. An example
is given for the `VectorSpace` interface, but all other interfaces
should have an equally detailed interface, which should be discussed
in the `issues` section of the git-hub repository.

Each contribution should be done on a **topic branch**, then a **pull
request** should be issued, which will be merged by me on mainline.

**No contribution will be accepted without a small unit test!**. Each
unit test should be written following
https://docs.python.org/2/library/unittest.html in the directory ./tests


- An abstract python interface of type `Shape`, used to describe the
  shape of a **computable object**. [TBD: interface]
  
- An abstract python interface of type `Position`, used to describe the
  position of a **computable object**. [TBD: interface]

- An abstract python interface of type `VectorSpace`, used to describe
   *one dimensional functions* on `[0,1]`, as *coefficients* times *basis
   functions*, with access to single basis functions, their derivatives,
   their support and  the  splitting of `[0,1]` into
   sub-intervals where *each basis* is assumed to be smooth. In
   particular, the following structure should be implemented for each
   type of basis:

	- `VectorSpace.n_dofs`:  the number of basis functions that span
	  the space (*degrees of freedom*)
	- `VectorSpace.n_cells`: the number of sub-intervals of `[0,1]`
      where each basis is smooth
	- `VectorSpace.cells`: the `n_cells+1` splitting points that make
      the `cells` of the `VectorSpace`
	- `VectorSpace.basis(i)`: the ith basis function (a callable
      function)
	- `VectorSpace.basis_der(i, d)`: the d-th derivative of the i-th
      basis function (a callable function)
	- `VectorSpace.basis_span(i)`: a tuple indicating the start and
      end indices into the `cells` object where the i-th basis
      function is different from zero
	- `VectorSpace.cell_span(i)`: an array of indices containing the
      basis functions which are non zero on the i-th `cell`
	- `VectorSpace.element(c)`: a callable function,
      representing `sum(c[i] * basis[i])`, which exploits the
      *locality* of the basis functions

- The implementation of the above for

	- Power basis functions
	- Continuous Lagrange (Newton-Cotes with end points) basis
      functions, of degree `n` with `N` repetitions
	- Bernstein basis functions of degree `n` with `N` repetitions
	- B-spline basis functions with given degree and knot_vector
	- NURBS basis functions with given degree and knot_vector

- An abstract python interface of type `TimeAnalysis`, capable of
   taking derivatives and integrals of **collections of paths**, given
   an actual `VectorSpace` object, and a matrix with
   `VectorSpace.n_dofs`  rows and an arbitrary number of columns,
   representing functions  of the given `VectorSpace`

- An `Interpolation` utility, taking a `VectorSpace`, `n_dofs` points,
  and constructing a solution `c` to the problem
  `sum_over_j(c[j]*b[j](x[j])) = f(x[i])`

- A `LeastSquare` utility, doing the same but with `n>n_dofs` points
  and using least squares

- A `MassMatrix` assembler, computing the sparse matrix $M_{ij} =
  \int_0^1 v_i(x) v_j(x) dx$ exploiting locality of the `VectorSpace`
  and a `Quadrature` formula given at construction time
  
- A `StiffnessMatrix` assembler, computing the sparse stiffness matrix
  $K_{ij} = \int_0^1 v'_i(x) v'_j(x) dx$ exploiting locality of the
  `VectorSpace` and a `Quadrature` formula given at construction time

- A `ArcLengthReparametrization`, taking a matrix of coefficients
  representing a curve in a `VectorSpace`, and creating a new one via
  a `LeastSquare` fit which satisfies `c'(x) = L` for each `x`

- A `CurveFromCurvature` function, which takes a vector function
  `g(x)` representing the curvature of a curve parametrized with arc
  length, and returns the curve satisfying `c(0) = 0`, `c'(x) = L` for
  `x in [0,1]` and `c''(x) = g(x)` 

- An abstract interface `EquationOfMotion`, taking a `Shape` and
  returning a `Position`, such that some underlying equations are
  satisfied [TBD: Interface]. For this interface, we will construct

	- collection of sphere swimmers 
	- filament swimmers using resistive force theory
	- crawlers

- An abstract optimization tool, taking an `EquationOfMotion`,
  computing a `CostFunction` associated to it, imposing a
  `ConstraintEquation` on it, and optimizing the `Shape` evolution, in
  order to obtain a `Shape` that satisfies the `ConstraintEquation`
  and minimizes the `CostFunction`


- A [Blender](www.blender.org) interface

	- given two files, containing numpy nd-arrays (as created by
	   `numpy.save`), representing the time evolution of the position
	   and shape, this interface should create an animation capable of
	   visualising the time evolution of the object. We will need
	   interfaces for the following objects
	   
	    - Golestanian Axis Swimmer (`m=1, n=2`)
		- Golestanian Plane Swimmer (`m=2, n=3`)
		- Golestanian Space Swimmer (`m=6, n=4`)
		- Axis crawler (`m=1, n=as many as you wish`)
		- Plane worm (`m=2, n=as many as you wish`)
		- Space worm (`m=6, n=as many as you wish`)
		- etc.

License:
------------------------------------------------------------

Please see the file ./LICENSE for details


Continuous Integration Status:
------------------------

[![Build Status](https://travis-ci.org/luca-heltai/ePICURE.png)](https://travis-ci.org/luca-heltai/ePICURE)
