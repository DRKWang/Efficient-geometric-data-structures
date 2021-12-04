# Efficient geometric data structures for storing and querying piecewise linear functions Project
The project focuses on applications of [**rtree**](https://en.wikipedia.org/wiki/R-tree) data structure.

<img src="https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/e269d580-8005-11ea-9c78-d4ddd0366ca6.png" width="500" height="300" />


#### Table of Contents

- [Package cgal-swig-bindings, script packages cgal, swig](#package-cgal-swig-bindings-script-packages-cgal-swig)
- [Package libspatialindex, rtree](#package-libspatialindex-rtree)
- [RealSet Class](#realSet-class)
- [RationalPolyhedralFan Class](#rationalpolyhedralfan-class)
- [Cut-generating functions](https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/Cut-generating-functions.ipynb)

#### Q&A

- [What is rtree?](#what-is-rtree)
- [What problems can it be used to solve?]

## Package cgal-swig-bindings, script packages cgal, swig
[CGAL](https://www.cgal.org/) is a software project that provides easy access to efficient and reliable geometric algorithms in the form of a C++ library. We integrated the package [cgal-swig-bindings](https://github.com/CGAL/cgal-swig-bindings) into the [SageMath](https://www.sagemath.org/) software for calling functions in CGAL. Code and discussion can be found in this [ticket](https://trac.sagemath.org/ticket/31098).

## Package libspatialindex, rtree
[libspatialindex](https://libspatialindex.org/en/latest/) is a software project that includes C++ implementation of R*-tree, an MVR-tree and a TPR-tree with C API, which are variants of rtree. [rtree](https://rtree.readthedocs.io/en/latest/index.html) is a  ctypes Python wrapper of libspatialindex that provides a number of advanced spatial indexing features for the spatially curious Python user. We integrated the package libspatialindex and into the [SageMath](https://www.sagemath.org/) software for the usage of rtree. Code and discussion can be found in this [ticket](https://trac.sagemath.org/ticket/32000).

## RealSet Class
[RealSet](https://doc.sagemath.org/html/en/reference/sets/sage/sets/real_set.html) in SageMath contains subsets of the real line that can be constructed as the union of a *finite* set of open and closed intervals. Compared to trivial storage, rtree data structure provides a fast path for retrieving the possible intersected intervals with accuracy, which could reduce the processing time of `contains()` and `intersection()` functions. In detail, noticing that two intervals intersect with each other only if their closure intersects with each other, we can organize all of the intervals of a given `Class RealSet()` in a one-dimensional rtree by storing their closures to overestimate the status of their intersection, then give a further verification of the intervals. Code and discussion can be found in this [ticket](https://trac.sagemath.org/ticket/32170#comment:9).

## RationalPolyhedralFan Class
A [rational polyhedral fan](https://doc.sagemath.org/html/en/reference/discrete_geometry/sage/geometry/fan.html) is a *finite* collection of **strictly** convex rational polyhedral cones, such that the intersection of any two cones of the fan is a face of each of them and each face of each cone is also a cone of the fan. For the `Class RationalPolyhedralFan()` in SageMath, We added rtree data structure for reducing the time of `support_contains()` function, which is used for checking if a point is contained in the support of the fan. The idea is similar to the situation in RealSet class, that we will give a preliminary overestimation of the search result first, then we give a further verification. In addition, we also add LP-solver and faceted-based method for automatically determining if it is a pointed fan or not. Code and discussion can be found in this [ticket](https://trac.sagemath.org/ticket/32170#comment:50).

## What is rtree?
rtree is a tree data structure used for queries and storage of multi-dimensional data. The key idea of the data structure is to group local objects and represent them with their minimum bounding rectangle in the next higher level of the tree; the "R" in R-tree is for rectangle. Another name for rtree is called AABB tree, Axis-Aligned Bounding Box Tree. Since all objects lie within this bounding rectangle, it is possible to make a fast query that does not intersect the bounding rectangle also cannot intersect any of the contained objects. Here is a friendly [introduction](https://www.azurefromthetrenches.com/introductory-guide-to-aabb-tree-collision-detection/).

## What are the ordinary operations for rtree data structure? 
See the [link](https://en.wikipedia.org/wiki/R-tree#Algorithm).
A prototypal list of the operations with inputs and outputs can be found [here](https://aabbtree.readthedocs.io/en/latest/).

## Is there any variants of rtree? What are they? 
Yes. The variants of it inlcude [PR-tree](https://en.wikipedia.org/wiki/Priority_R-tree), [R*-trees](https://en.wikipedia.org/wiki/R*-tree), [R+ tree](https://en.wikipedia.org/wiki/R%2B_tree), [Hilbert R-tree](https://en.wikipedia.org/wiki/Hilbert_R-tree), [X-tree](https://en.wikipedia.org/wiki/X-tree).

## What is the main difference of the variants?
Coverage is the entire area to cover all related rectangles. Overlap is the entire area which is contained in two or more nodes.

## What problems can rtree be used to solve in this project?
Regualarly, rtree, or AABB tree, can be used for 

## 

## Others

- [A quick way for computing the Minkowski sums](https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/Computation%20of%20Minkowski%20sums%20-%20Jupyter%20Notebook.pdf)
- [How to stop Sage from overriding Python number types](https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/How%20to%20stop%20Sage%20from%20overriding%20Python%20number%20types.md)
- [how to test libspatialindex](https://github.com/libspatialindex/libspatialindex/issues/218)
- [Other geometry library](https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/logfiles/log1.md)

