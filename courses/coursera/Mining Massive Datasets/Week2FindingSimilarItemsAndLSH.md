# Week 2
## Finding similar sets

## Applications
MMany data mining problems can be expressed as finding similar items. E.g.:

- Similar words in documents -> classification by topic
- Given a large set of documents, find documents similar -> detect plagiarism, avoid show the same information twice  
- Movies with similar fan sets or users with similar tastes -> recommender systems 
- Entity resolution

### Essential techniques to use

document -> |Shingling| -> sets -> |Minhashing| -> signatures -> | Locality Sensitive Hashing| -> candidate pairs to test for similarity


### Shingling: sets
Convert documents, emails, etc. to sets in order to convert a problem of documents similarity in one of sets intersection.

We will use k-shingles (sometimes called n-grams) or groups of k letters

```
Jaccard Similarity  = sets intersection / sets union
```

- Similar documents have similar k-shingles
- Change a word only affects k-shingles within distance k from the word and reordering paragraphs only affects the 2k shigles that cross paragraphs boundaries


#### Example: k-shingles

Find the set of 2-shingles for the "document":

ABRACADABRA
and also for the "document":
BRICABRAC

Answer the following questions:
- How many 2-shingles does ABRACADABRA have?
- How many 2-shingles does BRICABRAC have?
- How many 2-shingles do they have in common?
- What is the Jaccard similarity between the two documents"?

Then, find the true statement in the list below.

- The Jaccard similarity is 4/7.
- There are 5 shingles in common.
- ABRACADABRA has 5 2-shingles.
- The Jaccard similarity is 5/7.


### Minhashing: signatures
We can compress a long number of shingles hashing them to tokens with (say) 4 bytes. This way a document is represented by its tokens.
Two documents could (rarely) appear to have shingles in common when in fact only have in common the tokens.

Minhashing is a technique to convert large sets to short signatures preserving similarity. In order to achive this, we
can use a matrix representation of sets. In each row put one element of the set of all possibles k-shingles. In columns, put sets (documents)

| element (possible k-shingle) | set 1 (document 1) | set2 (document 2) |   |   |
|------------------------------|--------------------|-------------------|---|---|
| aba                          | 1                  | 0                 |   |   |
| abc                          | 0                  |                   |   |   |
|                              |                    |                   |   |   |

1 means the set (document) contains the element given by the row

Another example: rows could be products and columns could be customers represented by the set of products they bought


#### Example
Consider the following matrix:

|    | C1 | C2 | C3 | C4 |
|----|----|----|----|----|
| R1 | 0  | 1  | 1  | 0  |
| R2 | 1  | 0  | 1  | 1  |
| R3 | 0  | 1  | 0  | 1  |
| R4 | 0  | 0  | 1  | 0  |
| R5 | 1  | 0  | 1  | 0  |
| R6 | 0  | 1  | 0  | 0  |

Perform a minhashing of the data, with the order of rows: R4, R6, R1, R3, R5, R2. Which of the following is the correct
minhash value of the stated column? Note: we give the minhash value in terms of the original name of the row, rather
than the order of the row in the permutation. These two schemes are equivalent, since we only care whether hash values
for two columns are equal, not what their actual values are.

- The minhash value for C1 is R4
- The minhash value for C3 is R4
- The minhash value for C4 is R2
- The minhash value for C4 is R5


#### algorithm theory:
Let's order the rows in multiple permutations (say 100 or several hundred). For each row, we assign a minhash value to a
set Si given by minhash function h. The value is the element in the first row for which column has a non 0 value

```
Surprising property: the probability that the minhash function for a random permutation of rows produces 
the same value for two sets equals the Jaccard similarity of those sets. Thus, the expected similarity 
of two signatures equals the Jaccard similarity of the columns or sets that the signatures represent. 
And the longer the signatures, the smaller will be the expected error.  

For n permutations of rows we have n minhash functions: h1, h2, ..., hn. For the column representing set S, 
construct the minhash signature for S, the vector [h1(S), h2(S),...,hn(S)]. Thus, we can form a signature 
matrix in which the ith column of original matrix M is replaced by the minhash signature for the set in the 
ith column. Signature matrix will have only n rows and will be much smaller than the initial sparse matrix M
```

#### algorithm implementation for big data:
Permute a very large matrix M with millions or billions of rows is very time consuming. 

A good approximation to permuting rows: pick, say, 100 hash functions. For each column c and each hash function hi ,
keep a “slot” M(i, c). Intent: M(i, c) will become the smallest value of hi(r) for which column c has 1 in row r.
Note hi(r) is a hash (not minhash) function applied to the number of row, eg.:

hi(r) = x + 1 mod 5 

| row index | hi(r) |
|-----------|-------|
| 0         | 1     |
| 1         | 2     |
| 2         | 3     |
| 3         | 4     |
| 4         | 0     |

```
for each row r do begin
    for each hash function hi do
        compute hi (r);
    for each column c
        if c has 1 in row r
            for each hash function hi do
                if hi(r) is smaller than M(i, c) then
                    M(i, c) := hi(r);
end;
```

See computing method explained and examples in "Mining massive datasets" book, page 83 


## Locality-Sensitive Hashing 
Focus on those pairs of signatures likely to be similar. 

For signature matrices: hash columns several times. Similar columns are likely to hash to the same bucket. Candidate pairs
are those that hash at least one time to the same bucket.

Partition into bands, a big data implementation: 
- divide matrix M into b bands of r rows each one
- for each column, hash the portion of column in each band to one of k buckets. Make k as large as possible
- candidate columns pairs are those that hash to the same bucket for >=1 bands
- tune b and r to catch most of similar pairs but few non-similar pairs

### Example
Here is a matrix representing the signatures of seven columns, C1 through C7.

| C1 | C2 | C3 | C4 | C5 | C6 | C7 |
|----|----|----|----|----|----|----|
| 1  | 2  | 1  | 1  | 2  | 5  | 4  |
| 2  | 3  | 4  | 2  | 3  | 2  | 2  |
| 3  | 1  | 2  | 3  | 1  | 3  | 2  |
| 4  | 1  | 3  | 1  | 2  | 4  | 4  |
| 5  | 2  | 5  | 1  | 1  | 5  | 1  |
| 6  | 1  | 6  | 4  | 1  | 1  | 4  |

Suppose we use locality-sensitive hashing with three bands of two rows each. Assume there are enough buckets available
that the hash function for each band can be the identity function (i.e., columns hash to the same bucket if and only if
they are identical in the band). Find all the candidate pairs, and then identify one of them in the list below.

- C6 and C7
- C2 and C5
- C1 and C7
- C3 and C7


## Applications

### Entity resolution
The entity resolution problem is to examine a collection of records and determine which refer to the same entity.
Entities could be people, events, etc.
Typically, we want to merge records if their values in corresponding fields are similar.

E. g.: Detect records for the same person
Company A agreed to solicit customers for Company B, for a fee.
They then argued over how many customers.
Neither recorded exactly which customers were involved.

Each company had about 1 million records describing customers that might have been sent from A to B.
Records had name, address, and phone, but for various reasons, they could be different for the same person.

Problem: detect records equal in both companies

### Detect repeated news

Different newspapers take the same new from an agency and customize it: ads are added, some text is cut, put their brand, etc.

We observed thad ads have less stop words that news:
“Buy Sudzo” vs.“I recommend that you buy Sudzo for your laundry.”

So, define a shingle to be a stop word and the next two following words.
By requiring each shingle to have a stop word, they biased the mapping from documents to shingles so it picked more
shingles from the article than from the ads. Pages with the same article, but different ads, have higher Jaccard
similarity than those with the same ads, different articles.

## Distance measures
Generalized LSH is based on some kind of "distance" between points.

Jaccard similarity is not a distance. 1 - Jaccard distance is

Two major classes of distance measure:
- Euclidean

Euclidean space has some number of real value dimensions.
Euclidean distance is based on the locations of points in such space.
There is a notion of 'average' of two points

- Non-Euclidean
Distances are based on properties of points, but not their 'location' in a space

d is a 'distance measure' if is a function from pairs of points to real numbers such that:
d(x,y) > 0.
d(x,y) = 0 iff x = y.
d(x,y) = d(y,x)
d(x,y) < d(x,z) + d(z,y)

### Euclidean distances

- L2 norm is the most used distance:
d(x,y) = ((x1-x2)^ + (y1-y2)^) ^ 1/2

- L1 norm (Manhattan distance = distance if you had to travel along coordinates only)
d(x,y) = (x1-x2) + (y1-y2)

- L∞ norm:
d(x,y) = the maximum of the differences between x and y in any dimension ( what you get by taking the r th power of the
differences, summing and taking the r th root.)

### Non-euclidean distances
- Jaccard distance for sets = 1 minus Jaccard similarity.
- Cosine distance for vectors = angle between the vectors.
- Edit distance for strings = number of inserts and deletes to change one string into another.
- Hamming Distance for bit vectors = the number of positions in which they differ.

#### Example: Edit distance
The edit distance is the minimum number of character insertions and character deletions required to turn one string into
another. Compute the edit distance between each pair of the strings he, she, his, and hers. Then, identify which of the
following is a true statement about the number of pairs at a certain edit distance.

- There are 4 pairs at distance 5.
- There are 3 pairs at distance 4.
- There is 1 pair at distance 4.
- There are 2 pairs at distance 1.## Large scale machine learning. Nearest neighbors

### Supervised Learning

Objective: make a prediction
Estimating a function f(x) so that y=f(x)
Where y can be:
- Real number: Regression
- Category: Classification
- Complex object: ranking of items, parse tree, etc.

Process:
Estimate y = f(x) on training (X) and test (Y) sets
Check that the same f(x) also works on unseen X' and Y' data

In supervised learning data is labeled:
we have many pairs {(x,y)} where:
x is a vector of binary, real value or categorical features
y is a class ({+1, -1} or a real number)

#### Methods

Main question:
how to effectively train, that is build (or find) the parameters of a model?

##### k-nearest neighbors (instance based learning)

* Algorithm:
- Build a model from training set: {(x,y)}
- A query example (vector) q comes
- For each item x in vector, find closest examples x*
- Predict y*

* Works for regression and classification
* Collaborative filtering is a example of k-NN classifier:
- for a user x, find k most similar users that have rated movie y
- predict rating of x, yx, as an average of yk

* We need:
- A metric of distance: how to find nearest neighbours?
- How many neighbors look: k (in Kernel regression we look at all neighbours)
- An optional weighting function
- How to fit with the local points? (in k-Nearest neighbour just predict the average output among k nearest neighbors,
in 1-Nearest take the value of closest point)

Distance algorithm:
- given a set P of q points
- given a query point q:
    - NN: find the nearest neighbor p of q in P
    - range search: find one/all points within a distance of r from q

**Use locality sensitive hashing!**




##### Support Vector Machines (SVM)

##### Decision trees
