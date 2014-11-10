Clustering
==========
Given a set of points, the objetive is group them in k clusters or sets of points. The center of each cluster is called centroid  

How to do that in the context of massive datasets?. 

# K-means algorithm
- let's set k initial centroids chosen at random (later we'll see two better approaches for selection)
- for each point, assign it to nearest centroid
- recalculate new k centroids for this new distribution
- repeat two last steps until reach a stable distribution where clusters are stable (centroids doesn't change and points on't move between clusters)  

## How to choose k?
Calculate average distance from points to centroids and plot against k. We'll see a curve where average distance fall rapidly until right k, then falls slowly 

How to calculate the k initial centroids? 
- approach 1 "sampling": cluster a sample of the data using hierarchical cluster to obtain k clusters. This sample fit in memory
- approach 2 "pick dispersed set of points": pick the first point at random, choose the next point to be the one whose minimun distance to the selected points is as large as possible, repeat until we have k points  

## Order of complexity
With a naive implementation, cluster algorithms uses to have O(N^3). In k-means algorithm, it's O(k*N) being k the number of clusters and N the number of points

## Problem
This algorithm produces good clusters and it's linear for each round but can take a really large number of rounds

# Bradley-Fayyad-Reina (BFR) algorithm  
BFR algoritm can do the same that k-means clustering but in **only one pass over the data with a large dataset that doesn't fit in memory**. 

As all k-means algorithm it assumes an Euclidean space but makes also another strong assumption: that each cluster is normally distributed around a centroid in such a way that: 

- we can quantify the likelihood of finding a point in the cluster at a given distance from the centroid in each dimension 
- standard deviations in different dimensions may vary

This assumption implies that clusters look like axis-aligned ellipsis. 

The algorithm use three types of data sets: 
- **discard set (DS)**: points that can be assigned to a cluster. They are summarized and not being tracked anymore
- **compression set (CS)**: points that are close together but no close to any centroid. They are summarized but not assigned to any cluster
- **retained set (RS)**: isolated points waiting to be assigned to a compression set. These are the only that we are going to save between iterations 

For each chunk of points: 
- find those points that are "sufficiently close" to a cluster centroid
- assign them to the cluster and the DS
- update statistics of each cluster: 

```
N, number of points
SUMi, sum of point coordinates in each dimension 
SUMSQi, sum of square root of point coordinates in each dimension
si, i standard deviation is square root of (SUMSQi/N) - (SUMi/N)^2
```

- remaining points aren't close to any cluster. Use any in-memory clustering algorithm to cluster these points and the old RS. Clusters go to the CS, outlying points to the RS
- consider merging compressed sets in the CS
- if this is the last step, merge all compressed sets in the CS and all the RS points into their nearest cluster

Two questions: 
- **How to decide if a point is near enough to assign it to a cluster?**

Mahalanobis distance is used, it measures the likelihood of point belonging to currently nearest centroid 

```
Cluster has centroid C = (c1,...,cd) and standard deviations (s1,...,sd)
Point P = (x1,...,xd)

Normalized distance of point to centroid is: 
    yi = (xi-ci)/si

Mahalanobian distance of point P to centroid C is: 
    MD = square_root(sum(yi^2))
```

**Accept point P into cluster C if its MD is less than a threshold, e.g. 3*square_root(d)**

```
Rationale of this threshold: 
If a point is one standard deviation away from centroid then: 
    MD = square_root(d)
so, points with a MD <= square_root(d) are nearest than one standard deviation. Similarly: 
    3s<-> 3*square_root(d)
On the other hand, we know in a normal (gaussian) distribution: 
    68% of points are inside [-s, s] range
    95% are inside [-2s, 2s]
    99% are inside [-3s, 3s] <- this looks a threshold good enough 
```

- **How to decide if two compressed sets should be combined into one?**

Combine if **the combined variance is below some threshold** 

Many alternatives: consider dimensions diffrently, consider density 


