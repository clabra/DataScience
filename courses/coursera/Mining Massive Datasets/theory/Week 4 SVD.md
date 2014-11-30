Week 4
======

# SVD

```
SVD
A = U * E * Vt

A: huge but sparse
U, V: big and dense
E: sparse and small
```

# CUR

```
CUR
A = C * U * R

C, R: big but sparse
U: dense, but small
```

CUR is better to compute

# Questions

## Question 1
Note: In this question, all columns will be written in their transposed form, as rows, to make the typography simpler.
Matrix M has three rows and two columns, and the columns form an orthonormal basis. One of the columns is [2/7,3/7,6/7].
There are many options for the second column [x,y,z]. Write down those constraints on x, y, and z. Then, identi fy in
the list below the one column that could be [x,y,z]. All components are computed to three decimal places, so the
constraints may be satisfied only to a close approximation.

[.728, .485, -.485]
[.608, -.459, -.119]
[-.548, .401, .273]
[.429, .857, .286]

Question Explanation

The dot product of [2/7,3/7,6/7] and [x,y,z] must be 0
Also, the length of the vector [x,y,z] must be 1,


## Question 2
Note: In this question, all columns will be written in their transposed form, as rows, to make the typography simpler.
Matrix M has three rows and three columns, and the columns form an orthonormal basis. One of the columns is [2/7,3/7,6/7],
and another is [6/7, 2/7, -3/7]. Let the third column be [x,y,z]. Since the length of the vector [x,y,z] must be 1,
there is a constraint that x2+y2+z2 = 1. However, there are other constraints, and these other constraints can be used
to deduce facts about the ratios among x, y, and z. Compute these ratios, and then identify one of them in the list below.

y = -3z
y = 3z
x = 2y
2z = 3x

Question Explanation

The dot product of [x,y,z] with each of the two other columns must be 0.

## Question 3
Suppose we have three points in a two dimensional space: (1,1), (2,2), and (3,4). We want to perform PCA on these points,
so we construct a 2-by-2 matrix whose eigenvectors are the directions that best represent these three points. Construct
this matrix and identify, in the list below, one of its elements.

12
24
25
17

Question Explanation

Construct the matrix M whose columns correspond to the dimensions of the space and whose rows correspond to the points.
That is, M =
1	1
2	2
3	4

Compute Mt * M, which is
14	17
17	21

This is the matrix whose eigenvectors are the principal components.

## Question 4
Find, in the list below, the vector that is orthogonal to the vector [1,2,3]. Note: the interesting concept regarding
eigenvectors is "orthonormal," that is unit vectors that are orthogonal. However, this question avoids using unit vectors
to make the calculations simpler.
- [-4, 2, -1]
- [3, 0, -1]
- [-1, 1, -1]
- [-2, 3, -1]

Question Explanation

Vectors are orthogonal if and only if their dot product (sum of the products of their corresponding components) is 0.


