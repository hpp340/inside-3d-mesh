# inside-3d-mesh

This project demonstrates a short Python function to determine if a set of
points are inside or outside a 3d mesh. The 3d mesh is assumed to be made of
triangles which all have the same orientation. This function manages to be
short yet robust and reasonnably fast. No preprocessing of the mesh is required.

![splash screen](https://raw.githubusercontent.com/marmakoide/inside-3d-mesh/master/splash.png)

The implementation is using the Generalized Winding Number of a 3d mesh, a
concept introduced in the paper 
[Robust Inside-Outside Segmentation using Generalized Winding Numbers](http://igl.ethz.ch/projects/winding-number/)
by Alec Jacobson, Ladislav Kavan and Olga Sorkine-Hornung.

## Getting Started

### Prerequisites

You will need

* A Unix-ish environment
* Python 2.7 or above
* [Numpy](http://www.numpy.org)
* [Matplotlib](https://matplotlib.org)
* The [xz](https://en.wikipedia.org/wiki/Xz) compression suite


### Running the demos

Both demos 

1. load an ASCII STL file (a file format for 3d triangle mesh) from the
standard input
2. generates an uniform random sampling of the volume enclosed by the mesh
3. displays the samples

Using one the sample STL files provided with the naive implementation demo

```
xzcat meshes/fox.stl.xz | python demo-naive.py
```

Likewise, for the optimized implementation demo

```
xzcat meshes/fox.stl.xz | python demo-turbo.py
```

## Implementation notes

The naive implementation is a straight translation of the Generalized Winding 
Number definition. 

The optimized implementation uses a couple of tricks

* Vectorization : the winding number of all the points to test is accumulated 
per triangle, using Numpy vector operations. This allows to offload most of
the computations to compiled and optimized code.
* The 3x3 determinant is computed explicitly, so that it can be vectorized
and because Numpy's implementation is trading speed for robustness.
* The arctangent is not computed, as it is not required when for an 
inside/outside test.

Using the Generalized Winding Number for a inside/outside test is not optimal.
It takes O(n) operations for n triangles, while a raycasting approach takes 
O(log(n)) operations when a suitable data structure such as a KD-tree is used. 
However, the raycasting approach usually requires more code and takes more efforts 
to make it robust to degenerate cases. In contrast, the Generalized Winding Number 
approach can handles holes, non-manifold surfaces and duplicated triangles without
code for special case handling.

The Generalized Winding Number is naturally parallel, it is the same computation
repeated for each triangle and each point to test. This make this approach a
natural fit for a vectorized, multi-core implementation or a GPU implementation.

## Authors

* **Alexandre Devert** - *Initial work* - [marmakoide](https://github.com/marmakoide)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

