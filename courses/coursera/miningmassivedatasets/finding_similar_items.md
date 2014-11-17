#Finding similar sets

## Applications
Many data mining problems can be expressed as finding similar items. E.g.: 

- Similar words in documents -> classification by topic
- Given a large set of documents, find documents similar -> detect plagiarism, avoid show the same information twice  
- Movies with similar fan sets or users with similar tastes -> recommender systems 
- Entity resolution

## Essential techniques to use

document -> |Shingling| -> sets -> |Minhashing| -> signatures -> | Locality Sensitive Hashing| -> candidate pairs to test for similarity


## Shingling: sets
Convert documents, emails, etc. to sets in order to convert a problem of documents similarity in one of sets intersection. 

```
Jaccard Similarity  = sets intersection / sets union
```

- Similar documents have similar k-shingles
- Change a word only affects k-shingles within distance k from the word and reordering paragraphs only affects the 2k shigles that cross paragraphs boundaries  


## Minhashing: signatures
We can compress a long number of shingles hashing them to tokens with (say) 4 bytes. This way a document is represented by its tokens. Two documents could (rarely) appear to have shingles in common when in fact only have in common the tokens. 

Minhashing is a technique to convert large sets to short signatures preserving similarity. In order to achive this, we can use a matrix representation of sets. In each row put one element of the set of all possibles k-shingles. In columns, put sets (documents) 

element (possible k-shingle)  |   set 1 (document 1)  |   set2 (document 2)  |   
-- | -- | -- 
aba | 0 | 1
aca | 0 | 0

1 means the set (document) contains the element given by the row

Another example: rows could be products and columns could be customers represented by the set of products they bought

- algorithm in theory: 
Let's order the rows in multiple permutations (say 100 or several hundred). For each row, we assign a minhash value to a set Si given by minhash function h. The value is the element in the first row for which column has a non 0 value  

```
Surprising property: the probability that the minhash function for a random permutation of rows produces the same value for two sets equals the Jaccard similarity of those sets. Thus, the expected similarity of two signatures equals the Jaccard similarity of the columns or sets that the signatures represent. And the longer the signatures, the smaller will be the expected error.  

For n permutations of rows we have n minhash functions: h1, h2, ..., hn. For the column representing set S, construct the minhash signature for S, the vector [h1(S), h2(S),...,hn(S)]. Thus, we can form a signature matrix in which the ith column of original matrix M is replaced by the minhash signature for the set in the ith column. Signature matrix will have only n colums and will be much smaller than the initial sparse matrix M
```

- algorithm implementation: 
Permute a very large matrix M with millions or billions of rows is very time consuming. 

A good approximation to permuting rows: pick, say, 100 hash functions. For each column c and each hash function hi , keep a “slot” M(i, c). Intent: M(i, c) will become the smallest value of hi(r) for which column c has 1 in row r. Note hi(r) is a hash (not minhash) function applied to the number of row, eg.: 

hi(r) = x + 1 mod 5 
row |   hi(r)
0       1
1       2
2       3
3       4
4       0

See computing method explained and examples in "Mining massive datasets" book, page 83 

for each row r do begin
    for each hash function hi do
        compute hi (r);
    for each column c
        if c has 1 in row r
            for each hash function hi do
                if hi(r) is smaller than M(i, c) then
                    M(i, c) := hi(r);
end;


## Locality-Sensitive Hashing 
Focus on those pairs of signatures likely to be similar. For signature matrices: hash columns to many buckets, and make elements of the same bucket candidate pairs 

Two columns c and d are a candidate pair if, and only if, their signatures agree in at least fraction t of the rows
