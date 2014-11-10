Clustering
==========
Given a set of points, the objetive is group them in k clusters or sets of points. The center of each cluster is called centroid  

How to do that in the context of massive datasets?. 

# K-means algorithm
- let's set k initial centroids chosen at random (later we'll see two better approaches for selection)
- for each point, assign it to nearest centroid
- recalculate new k centroids for this new distribution
- repeat two last steps until reach a stable distribution where clusters are stable (centroids doesn't change and points on't move between clusters)  

# How to choose k?
Calculate average distance from points to centroids and plot against k. We'll see a curve where average distance fall rapidly until right k, then falls slowly 

* How to calculate the k initial centroids? 
- approach 1 "sampling": cluster a sample of the data using hierarchical cluster to obtain k clusters. This sample fit in memory
- approach 2 "pick dispersed set of points": pick the first point at random, choose the next point to be the one whose minimun distance to the selected points is as large as possible, repeat until we have k points  

# Order of complexity
With a naive implementation, cluster algorithms uses to have O(N^3). In k-means algorithm, it's O(k*N) being k the number of clusters and N the number of points

