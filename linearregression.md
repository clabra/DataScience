Linear regression
=======================

Notes taken from Standford's "Machine Learning" course by Andrew Ng

The objective of linear regression is to minimize the cost function: 

```
J(θ)= 1/(2*m) * Sum (hθ(x(i))−y(i))^2
```

```
hθ(x) = θ0+θ1*x1 = Trans θ * x
```

The parameters of your model are the θj values. These are the values you searh for.  

Two methods: 
  * Gradient descent algorithm (when number of features is much more than 100.0000)
  * Normal equations (when number of features x are < 100.000)
