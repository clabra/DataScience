PageRank
========

# Week 1

## Basic PageRank calculation: Transition Matrix

The objective is calculate the PageRank of each node in a web. 

Each node can have in and out links to other node. Imagine a surfer that is walking the web going from a node to a next linked node. We can define the Transition Matrix of the web describing what happens to random surfers after one step. This matrix M has n rows and columns, if there are n pages. 

The simplest formula is: 
```
v′ = M * v

M: element mij in row i and column j has value 1/k if page j has k arcs out, and one of them is to page i. Otherwise, mij = 0.

```

E.g.: In a web with A, B, C nodes, first column in M represents the probability that a surfer at A has to be in each of the other nodes in next iteration (0 or 1/out_links).  

Iterate for about 50 times and the last values of vector v´ are the PageRanks of nodes

## Teleporting 

In real world a more complex version of previous formula is use to avoid problems with: 

- 'dead ends'
- 'spider traps'

We use 'taxation', a calculation of PageRank allowing each random surfer a small probability of teleporting to a random page, rather than following an out-link from their current page. The iterative step, where we compute a new vector estimate of PageRanks v′ from the current PageRank estimate v and the transition matrix M is

```
v′ = β * M * v + (1−β) * e/n

β is a chosen constant, usually in the range 0.8 to 0.9 
e is a vector of all 1’s
n is the number of nodes in the web graph

βMv represents the case where, with probability β, the random surfer decides to follow an out-link from their present page

(1 − β)e/n is a vector each of whose components has value (1 − β)/n and represents the introduction, with probability 1 − β, of a jump to a random page.
```
