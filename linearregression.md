Linear regression
=======================

Notes taken from Standford's "Machine Learning" course by Andrew Ng

The objective of linear regression is to minimize the cost function: 

```
J(θ)= 1/(2*m) * Sum (hθ(x(i))−y(i))^2
```

```
hθ(x) = θ0 + θ1 * x1 = Trans θ * X
```

xj are the features of your model and yi the training samples. E.g: yi=prize of house i depends of x1=size, x2=age

The parameters of your model are the θj values. These are the values you search for.  

Two methods: 
  * Gradient descent algorithm (when number of features xi is much more than 100.0000)
  * Normal equations (when number of features xi much less than < 100.000)
