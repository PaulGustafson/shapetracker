**Definition 1:** A **shape** is a tuple $d = (d_1, \ldots, d_n) \in \mathbb{N}^n$ for some $n \in \mathbb{N}$. To every shape $d$, we associate an index set 
$I(d) = \mathbb{Z}_{d_1} \times \cdots \mathbb{Z}_{d_n}$.

**Definition 2:** A **shapetracker** $(d, f, M)$ consists of a shape $d$, a map $f: I(d) \to \mathbb{N}$, and a mask $M \subset I(d)$.

**Definition 3:** A **view** $(d, s, o, a, b)$ consists of:
- a shape $d \in \mathbb{N}^n$ for some $n \in \mathbb{N}$,
- strides $s \in \mathbb{Z}^n$,
- an offset $o \in \mathbb{N}$, and
- lower and upper masks $a,b \in \mathbb{Z}_{d_1} \times \cdots \mathbb{Z}_{d_n}$ with $a_k \le b_k$ for all $k$.

To every view $(d, s, o, a, b)$, we associate a canonical shapetracker $(d, f, M)$ where $f: I(d) \to \mathbb{N}$ is given by the stride map

$$f(i_1, \ldots, i_n) = o + \sum_k s_k i_k$$

and we have the rectangular mask

$$M = \prod_k [a_k, b_k] \subset I(d)$$

In addition, to every shape $d$ we associate a canonical view $(d, s, 0, (0)_k, (d_k-1)_k )$ where $s = \prod_{j > k} d_k$. In this case, the corresponding stride map $f$ induces a bijection onto $\left[0, \prod d_k \right) \subset \mathbb{N}$. We define $g_d : \left[0, \prod d_k \right) \to I(d)$ to be the inverse to this map.

# Movement operations

There are six movement ops on shapetrackers -- pad, shrink, expand, permute, stride, reshape.

- "Pad" adds coordinates before and after each dimension, and masks off the new coordinates.
- "Shrink" is the inverse operation -- it cuts out coordinates at the beginning and end of each dimension.
- "Expand" broadcasts a tensor along singleton-dimensions to a larger one.
- "Permute" is a generalization of the matrix transpose -- it applies a permutation to the dimensions.
- "Reshape" is the only complicated operation -- to reshape a shapetracker $(d, f, M)$ into a new shapetracker $(d', f', M')$ with $\prod_k d_k = \prod_j d_j'$, we return a new shapetracker

$$(f \circ g_{d} \circ f', \quad  M' \cap (g_{d'} \circ f)(M))$$

We say an ordered pair of views $(v_1, v_2)$ is **mergeable** if there exists a view $v_3$ such that reshaping the canonical shapetracker for $v_1$ into the canonical shapetracker for $v_2$ returns a shapetracker equal to the canonical shapetracker for $v_3$.
