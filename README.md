# Efficient geometric data structures for storing and querying piecewise linear functions Project
The project focuses on applications of [**rtree**](https://en.wikipedia.org/wiki/R-tree) data structure.

<img src="https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/e269d580-8005-11ea-9c78-d4ddd0366ca6.png" width="500" height="300" />

#### **Projects**

- [Package cgal-swig-bindings, script packages cgal, swig](#package-cgal-swig-bindings-script-packages-cgal-swig)
- [Package libspatialindex, rtree](#package-libspatialindex-rtree)
- [RealSet Class](#realSet-class)
- [RationalPolyhedralFan Class](#rationalpolyhedralfan-class)
- [Cut-generating functions](https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/Cut-generating-functions.ipynb)

#### **Q&A**

- [What is rtree?](#what-is-rtree)
- [What are the ordinary operations for rtree data structure?](#what-are-the-ordinary-operations-for-rtree-data-structure)
- [Is there any variants of rtree? What are they?](#is-there-any-variants-of-rtree-what-are-they)
- [What is the main difference of those variants?](#what-is-the-main-difference-of-those-variants)
- [What problems can rtree be used to solve in this project?](#what-problems-can-rtree-be-used-to-solve-in-this-project)
- [What are the contributions of this project?](#what-are-the-contributions-of-this-project)
- [How to access to those work?](#how-to-access-to-those-work)

#### **Installation & Usage**
- [cgal_swig_bindings, cgal, swig](#cgal_swig_bindings-cgal-and-swig-package)
- [libspatialindex and rtree](#cgal_swig_bindings-cgal-and-swig-package)
- [RealSet_rtree Class](#realset_rtree-class)
- [RationalPolyhedralFan_rtree Class](#rationalpolyhedralfan-class-1)

# Projects

## Package cgal-swig-bindings, script packages cgal, swig
[CGAL](https://www.cgal.org/) is a software project that provides easy access to efficient and reliable geometric algorithms in the form of a C++ library. We integrated the package [cgal-swig-bindings](https://github.com/CGAL/cgal-swig-bindings) into the [SageMath](https://www.sagemath.org/) software for calling functions in CGAL. Code and discussion can be found in this [ticket](https://trac.sagemath.org/ticket/31098).

## Package libspatialindex, rtree
[libspatialindex](https://libspatialindex.org/en/latest/) is a software project that includes C++ implementation of R*-tree, an MVR-tree and a TPR-tree with C API, which are variants of rtree. [rtree](https://rtree.readthedocs.io/en/latest/index.html) is a  ctypes Python wrapper of libspatialindex that provides a number of advanced spatial indexing features for the spatially curious Python user. We integrated the package libspatialindex and into the [SageMath](https://www.sagemath.org/) software for the usage of rtree. Code and discussion can be found in this [ticket](https://trac.sagemath.org/ticket/32000).

## RealSet Class
[RealSet](https://doc.sagemath.org/html/en/reference/sets/sage/sets/real_set.html) in SageMath contains subsets of the real line that can be constructed as the union of a *finite* set of open and closed intervals. Compared to trivial storage, rtree data structure provides a fast path for retrieving the possible intersected intervals with accuracy, which could reduce the processing time of `contains()` and `intersection()` functions. In detail, noticing that two intervals intersect with each other only if their closure intersects with each other, we can organize all of the intervals of a given `Class RealSet()` in a one-dimensional rtree by storing their closures to overestimate the status of their intersection, then give a further verification of the intervals. Code and discussion can be found in this [ticket](https://trac.sagemath.org/ticket/32170#comment:9).

## RationalPolyhedralFan Class
A [rational polyhedral fan](https://doc.sagemath.org/html/en/reference/discrete_geometry/sage/geometry/fan.html) is a *finite* collection of **strictly** convex rational polyhedral cones, such that the intersection of any two cones of the fan is a face of each of them and each face of each cone is also a cone of the fan. For the `Class RationalPolyhedralFan()` in SageMath, We added rtree data structure for reducing the time of `support_contains()` function, which is used for checking if a point is contained in the support of the fan. The idea is similar to the situation in RealSet class, that we will give a preliminary overestimation of the search result first, then we give a further verification. In addition, we also add LP-solver and faceted-based method for automatically determining if it is a pointed fan or not. Code and discussion can be found in this [ticket](https://trac.sagemath.org/ticket/32170#comment:50).

# Q&A

## What is rtree?

Short answer:

**rtree** is a tree data structure used for queries and storage of multi-dimensional data. The key idea of the data structure is to group local objects and represent them with their minimum bounding rectangle in the next higher level of the tree; the "r" in rtree is for rectangle. Another name for rtree is called AABB tree, Axis-Aligned Bounding Box Tree. Since all objects lie within this bounding rectangle, it is possible to make a fast query that does not intersect the bounding rectangle also cannot intersect any of the contained objects.

A friendly introduction can be found [here](https://www.azurefromthetrenches.com/introductory-guide-to-aabb-tree-collision-detection/). 

A little long slides but with clear details can be found [here](https://box2d.org/files/ErinCatto_DynamicBVH_GDC2019.pdf).

## What are the ordinary operations for rtree data structure? 
See this [link](https://en.wikipedia.org/wiki/R-tree#Algorithm).
A simple prototype with inputs and outputs can be found [here](https://aabbtree.readthedocs.io/en/latest/).

## Is there any variants of rtree? What are they? 
Yep. 

The variants of it inlcude [PR-tree](https://en.wikipedia.org/wiki/Priority_R-tree), [R*-trees](https://en.wikipedia.org/wiki/R*-tree), [R+ tree](https://en.wikipedia.org/wiki/R%2B_tree), [Hilbert R-tree](https://en.wikipedia.org/wiki/Hilbert_R-tree), [X-tree](https://en.wikipedia.org/wiki/X-tree).

## What is the main difference of those variants?
There are two important concepts for rtree:

**Coverage** is the entire area to cover all related rectangles.

**Overlap** is the entire area that is contained in two or more nodes.

Minimal coverage reduces the amount of "dead space" (empty area) which is covered by the nodes of the R-tree. Minimal overlap reduces the set of search paths to the leaves (even more critical for the access time than minimal coverage). Efficient search requires minimal coverage and overlap.

The main *difference* between those variants lies in how they deal with the Coverage and Overlap. For example, R+ trees differ from R trees in that: nodes are not guaranteed to be at least half-filled, the entries of any internal node do not overlap, and an object ID may be stored in more than one leaf node.

## What problems can rtree be used to solve in this project?
In this project, we use the rtree to solve the problem of intersection detection of convex [polytopes](https://en.wikipedia.org/wiki/Polytope) in a high-dimensional space for mathematical research. Because most mathematicians in this area only focus on pure theoretical research with less attention on how to organize them for computation, it is common to store convex polytopes in the element-to-set form without any interior relationship support. However, as the number of convex polytopes increases, the intersection detection will be slow since it is a linear scanning due to the element-to-set form. The advantage of rtree is that it can store those convex polytopes based on their geometrical neighborhood in a tree organizing, which is a binary search for queries.

We have implemented rtree data structure for RealSet class, (It can be seen as a collection of one-dimensional convex polytopes), and for pointed RationalPolyhedronFan class, (Even though it is a collection of cones, we transformed cones to convex polytopes so that those cones still can be stored in a rtree) for obtaining high speed. See codes of class [RealSet_rtree](https://git.sagemath.org/sage.git/tree/src/sage/sets/real_set.py?id=dfc5410a11c9e32eabf13648f4bce7dd20552cb4), and class [RationalPolyhedralFan_rtree](https://git.sagemath.org/sage.git/tree/src/sage/geometry/fan.py?id=ebe6d929ee4bf48834dc1ea51d7fe51b65469880). The details of implementation can be found on this [page](https://trac.sagemath.org/ticket/32170).

## What are the contributions of this project?
Methods:
- Method for automatically determining whether a Polyhedral fan is pointed or not, see comments[50-53](https://trac.sagemath.org/ticket/32170#comment:50).
- Method for quickly computing Minkowski sums of a group of convex polytopes, see this [page](https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/Computation%20of%20Minkowski%20sums%20-%20Jupyter%20Notebook.pdf).
- Method for quickly detecting the intersection of polytope pairs, see this [page](https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/part2%20and%20part3%20-%20Jupyter%20Notebook.pdf).

Implementations:
- Class [RealSet_rtree](https://git.sagemath.org/sage.git/tree/src/sage/sets/real_set.py?id=dfc5410a11c9e32eabf13648f4bce7dd20552cb4)
- Class [RationalPolyhedralFan_rtree](https://git.sagemath.org/sage.git/tree/src/sage/geometry/fan.py?id=ebe6d929ee4bf48834dc1ea51d7fe51b65469880)

Packaging:
- [Package cgal-swig-bindings, script packages cgal, swig](https://trac.sagemath.org/ticket/31098)
- [Package libspatialindex, rtree](https://trac.sagemath.org/ticket/32000)

## How to access to those work?
You may access them via SageMath software. You can check the tutorial of SageMath from this [page](https://doc.sagemath.org/html/en/tutorial/).

# Installation & Usage

The following code has been installed successfully on MAC OS. It might not be installed successfully on other systems.

## cgal_swig_bindings, cgal and swig package

To get access to cgal in sage, please do as follows.

1. Install Sage from source.

Since *cgal_swig_bindings* package currently is waiting for review, it can only be accessed via develop version, instead of a release version, we have to install it from source. To install it, see [this page](https://doc.sagemath.org/html/en/installation/source.html#step-by-step-installation-procedure).

2. Install CGAL.

Run the following script in terminal:

```$brew install cgal```

3. Install SWIG.

Run the following script in terminal:

```$brew install swig```

4. Install cgal_swig_bindings.

Enter into **SAGE_ROOT** in terminal:

```$cd path/to/SAGE_ROOT```

Pull the corresponding versions from sage git, which includes the scripts for installing cgal_swig_bindings.

```$git pull trac u/mkoeppe/cgal_swig_bindings```

Install it via sage:

```$./sage -i cgal_swig_bindings```

Now, we are done.

To test whether it has been installed successfully, launch sage in terminal:

```$./sage```

Then import cgal in sage:

```sage: import CGAL```

If it can be imported without any error, then it shows installation is successful.

To use it properly, please see these [examples](https://github.com/CGAL/cgal-swig-bindings/wiki/BindingsExamples).

## libspatialindex and rtree package

To get access to rtree in sage, please do as follows.

1. Install sage from source.

Since *cgal_swig_bindings* package currently is waiting for review, it can only be accessed via develop version, instead of a release version, we have to install it from source. To install it, see [this page](https://doc.sagemath.org/html/en/installation/source.html#step-by-step-installation-procedure).

2. Install libspatialindex and rtree in sage.

Enter into **SAGE_ROOT** in terminal:

```$cd path/to/SAGE_ROOT```

Pull the corresponding versions from sage git, which includes the scripts for installing libspatialindex and rtree:

```$git pull trac public/32000```

Install libspatialindex and rtree via sage:

```
$./sage -i libspatialindex
$./sage -i rtree
```

Now, we are done.

To test whether it has been installed successfully, launch sage in terminal:

```$./sage```

Then try to import rtree in sage:

```sage: from rtree import index```

If it can be imported without any error, then it shows installation is successful.

To use it properly, please see these [examples](https://rtree.readthedocs.io/en/latest/tutorial.html).

## RealSet_rtree Class

To get access to `RealSet_rtree` class in sage, please do as follows.

1. Install sage from source.

Since *cgal_swig_bindings* package currently is waiting for review, it can only be accessed via develop version, instead of a release version, we have to install it from source. To install it, see [this page](https://doc.sagemath.org/html/en/installation/source.html#step-by-step-installation-procedure).

2. Install libspatialindex and rtree in sage.

Pull the corresponding versions from sage git:

```$git pull trac public/32170```

Install libspatialindex and rtree via sage:

```
$./sage -i libspatialindex
$./sage -i rtree
```

Recompile files in sage:

```$./sage -b```

Now, we are done.

To test whether it has been installed successfully, launch sage in terminal:

```$./sage```

Then try to import it in sage:

```sage: from sage.sets.real_set import RealSet_rtree```

If it can be imported without any error, then installation is successful.

To use it, see the following examples.

1. Define a RealSet_rtree class:

```
sage: from sage.sets.real_set import RealSet_rtree
sage: A = RealSet_rtree(0,1)
```

2. Check whether a point is included in a real set class or not.

```
sage: from sage.sets.real_set import RealSet_rtree
sage: RealSet_rtree(0,1).contains(0.5)              
True
sage: RealSet_rtree(0,1).contains(3)                                                                
False
sage: (RealSet_rtree(0,1)+RealSet_rtree(3,4)).contains(3)                                           
False
sage: (RealSet_rtree(0,1)+RealSet_rtree([3,4])).contains(3)                                         
True
```

3. The type of the intersection of two `RealSet_rtree` instances is still maintained.

```
sage: from sage.sets.real_set import RealSet_rtree 
sage: A = RealSet(0,1)
sage: B = RealSet_rtree(0.5,3)
sage: print(type(A.intersection(B)), type(B.intersection(A)))
<class 'sage.sets.real_set.RealSet_with_category'> <class 'sage.sets.real_set.RealSet_rtree_with_category'>
```

Other examples can be found in [docstring](https://git.sagemath.org/sage.git/tree/src/sage/sets/real_set.py?id=a2fa0baaf7168d67a75f290640ad86cda2d75ad1) of `RealSet_rtree` class.

## RationalPolyhedralFan_rtree Class

To get access to `RationalPolyhedralFan_rtree` class in sage, please do as follows.

1. Install sage from source.

Since *cgal_swig_bindings* package currently is waiting for review, it can only be accessed via develop version, instead of a release version, we have to install it from source. To install it, see [this page](https://doc.sagemath.org/html/en/installation/source.html#step-by-step-installation-procedure).

2. Install libspatialindex and rtree in sage.

Pull the corresponding versions from sage git:

```$git pull trac public/32170```

Install libspatialindex and rtree via sage:

```
$./sage -i libspatialindex
$./sage -i rtree
```

Recompile files in sage:

```$./sage -b```

Now, we are done.

To test whether it has been installed successfully, launch sage in terminal:

```$./sage```

Then try to import it in sage:

```sage: from sage.geometry.fan import RationalPolyhedralFan_rtree```

If it can be imported without any error, then installation is successful.

To use it, see the following examples.

1. Define a `RationalPolyhedralFan_rtree` class:

Explicitly define it with `RationalPolyhedralFan_rtree` class,
```
sage: from sage.geometry.fan import RationalPolyhedralFan_rtree
sage: v1, v2= vector([0,1]), vector([1,0])
sage: v1.set_immutable()
sage: v2.set_immutable()
sage: f = RationalPolyhedralFan_rtree([(0,),(1,)], [v1,v2], None)
```

Or, define it from `Fan`, `FaceFan`, `NormalFan` and `Fan2d` function, with setting `allow_rtree = True`.

```
sage: from sage.geometry.fan import RationalPolyhedralFan_rtree
sage: cone1 = Cone([(0,-1), (1,0)])
sage: cone2 = Cone([(1,0), (0,1)])
sage: f = Fan([cone1, cone2], allow_rtree = True)
```

2. Check whether a fan is pointed or not.
```
sage: from sage.geometry.fan import RationalPolyhedralFan_rtree
sage: v1, v2= vector([0,1]), vector([1,0])
sage: v1.set_immutable()
sage: v2.set_immutable()
sage: f = RationalPolyhedralFan_rtree([(0,),(1,)], [v1,v2], None)
sage: f.is_pointed()
True
```

3. Check whether a point is included in a `RationalPolyhedralFan_rtree` class or not.

```
sage: from sage.geometry.fan import RationalPolyhedralFan_rtree
sage: cone1 = Cone([(0,-1), (1,0)])
sage: cone2 = Cone([(1,0), (0,1)])
sage: f = Fan([cone1, cone2], allow_rtree = True)
sage: f.support_contains(f.lattice()(1,0))
True
sage: f.support_contains(cone1)    # a cone is not a point of the lattice
False
sage: f.support_contains((1,0))
True
sage: f.support_contains(1,1)
True
sage: f.support_contains((-1,0))
False
```
Other examples can be found in [docstring](https://git.sagemath.org/sage.git/tree/src/sage/geometry/fan.py?id=05ea99da812dd754edd97a717ae1c18eeeec1636) of `RationalPolyhedralFan_rtree` class.

# Other useful references

- [A quick way for computing the Minkowski sums](https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/Computation%20of%20Minkowski%20sums%20-%20Jupyter%20Notebook.pdf)
- [How to stop Sage from overriding Python number types](https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/How%20to%20stop%20Sage%20from%20overriding%20Python%20number%20types.md)
- [How to test libspatialindex](https://github.com/libspatialindex/libspatialindex/issues/218)
- [Other geometry library](https://github.com/DRKWang/Efficient-geometric-data-structures/blob/main/logfiles/log1.md)

