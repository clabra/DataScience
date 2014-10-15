PageRank
========
The objective is calculate the PageRank of each node in a web. 

Each node can have in and out links to other nodes

We are going to use the Transition Matrix. We can define the transition matrix of the Web to describe what happens to random surfers after one step. This matrix M has n rows and columns, if there are n pages. The element mij in row i and column j has value 1/k if page j has k arcs out, and one of them is to page i. Otherwise, mij = 0.

E.g.: In a web with A, B, C nodes, first column in M represents the probability that a surfer at A has to be in each of the other nodes in next iteration (0 or 1/out_links).  

The simplest formulation is iterate next for about 50 times: 
v′ = M * v

The last values of v[i+1] are the PageRanks of nodes


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


In real world a more complex version of previous formula is use to avoid problems with 'dead ends' and 'spider traps': we use 'taxation', a calculation of PageRank by allowing each random surfer a small probability of teleporting to a random page, rather than following an out-link from their current page. The iterative step, where we compute a new vector estimate of PageRanks v′ from the current PageRank estimate v and the transition matrix M is
  v′ = β * M * v + (1−β) * e/n
  where β is a chosen constant, usually in the range 0.8 to 0.9, e is a vector of all 1’s with the appropriate number of components, and n is the number of nodes in the Web graph. The term βMv represents the case where, with probability β, the random surfer decides to follow an out-link from their present page. The term (1 − β)e/n is a vector each of whose components has value (1 − β)/n and represents the introduction, with probability 1 − β, of a new random surfer at a random page.
