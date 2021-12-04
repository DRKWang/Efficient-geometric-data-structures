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

## Q&A 

### What is rtree?
Short answer: **rtree** is a tree data structure used for queries and storage of multi-dimensional data. The key idea of the data structure is to group local objects and represent them with their minimum bounding rectangle in the next higher level of the tree; the "r" in rtree is for rectangle. Another name for rtree is called AABB tree, Axis-Aligned Bounding Box Tree. Since all objects lie within this bounding rectangle, it is possible to make a fast query that does not intersect the bounding rectangle also cannot intersect any of the contained objects.

A friendly introduction can be found [here](https://www.azurefromthetrenches.com/introductory-guide-to-aabb-tree-collision-detection/). a little long slides but with clear details can be found [here](https://box2d.org/files/ErinCatto_DynamicBVH_GDC2019.pdf).

### What are the ordinary operations for rtree data structure? 
See this [link](https://en.wikipedia.org/wiki/R-tree#Algorithm).
Prototypal operations with inputs and outputs can be found [here](https://aabbtree.readthedocs.io/en/latest/).

### Is there any variants of rtree? What are they? 
Yes. The variants of it inlcude [PR-tree](https://en.wikipedia.org/wiki/Priority_R-tree), [R*-trees](https://en.wikipedia.org/wiki/R*-tree), [R+ tree](https://en.wikipedia.org/wiki/R%2B_tree), [Hilbert R-tree](https://en.wikipedia.org/wiki/Hilbert_R-tree), [X-tree](https://en.wikipedia.org/wiki/X-tree).

### What is the main difference of those variants?
There are two important concepts for rtree. **Coverage** is the entire area to cover all related rectangles. **Overlap** is the entire area which is contained in two or more nodes. Minimal coverage reduces the amount of "dead space" (empty area) which is covered by the nodes of the R-tree. Minimal overlap reduces the set of search paths to the leaves (even more critical for the access time than minimal coverage). Efficient search requires minimal coverage and overlap. The main *difference* of those variants lies on how they deal with the Coverage and Overlap. For example, R+ trees differ from R trees in that: nodes are not guaranteed to be at least half filled, the entries of any internal node do not overlap, and an object ID may be stored in more than one leaf node.

### What problems can rtree be used to solve in this project?
In this project, we use the rtree to solve the problem of intersection detection of [polytopes](https://en.wikipedia.org/wiki/Polytope) in a high-dimensional space for mathematical research. Because most mathematicans in this area only focus on the pure theoritical research with less attention on how to organize them for computation, it is common to store polytopes in the element-to-set form without any interior relationship support. However, as the number of polytopes increases, the intersection dectection will be slow since it is a linear scanning due to element-to-set form. The advantage of rtree is that it can store those polytopes based on their geometrical neighbourhood in a tree organizing, which is a binary search for querying. We have implemented rtree data structure for RealSet class, (It can be seen as a collection of one-dimensional polytopes), and for pointed RationalPolyhedronFan class, (Even though it is a collection of cones, we can transform cones to polytopes so that those cones can also be stored in a rtree) for obtaining faster speed. 
The code of implementations can be found in those links [Class RealSet_rtree()](https://git.sagemath.org/sage.git/tree/src/sage/sets/real_set.py?id=dfc5410a11c9e32eabf13648f4bce7dd20552cb4), and [Class RationalPolyhedronFan] (https://git.sagemath.org/sage.git/tree/src/sage/geometry/fan.py?id=ebe6d929ee4bf48834dc1ea51d7fe51b65469880). The details of it can found in this [page](https://trac.sagemath.org/ticket/32170)

### 

## Other references

- [A quick way for computing the Minkowski sums](https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/Computation%20of%20Minkowski%20sums%20-%20Jupyter%20Notebook.pdf)
- [How to stop Sage from overriding Python number types](https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/How%20to%20stop%20Sage%20from%20overriding%20Python%20number%20types.md)
- [how to test libspatialindex](https://github.com/libspatialindex/libspatialindex/issues/218)
- [Other geometry library](https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/logfiles/log1.md)

