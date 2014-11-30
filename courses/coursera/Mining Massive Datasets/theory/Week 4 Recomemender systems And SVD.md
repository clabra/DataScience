Week 4
======

# Recommender Systems

## Content based

From scarcity to abundance. The 'long tail'

More choices necessitates better filters, e.g. recommendation engines

Recommendation of items (books, movies) or people (friend recommendations in Facebook, LinkedIn, Twitter)

Types of recommendations:

- Editorial and hand curated: lists of 'favorites', 'essentials', etc.
- Simple aggregates: 'top 10', 'most popular', 'recent uploads', etc.
- Tailored to individual users: Amazon, Netflix, Pandora. Our focus is here.

### Formal Model

```
u is an utility function:

u: C X S -> R

C, set of customers
S, set of items to recommend
R, set of ratings, set totally ordered (e.g.: 0-5 stars rating)

U is the utility matrix:
customers: rows,
items: columns
rating: value
```

Key problems:
- how to collect the data in the utility matrix (gathering known ratings)
- extrapolate unknown ratings from the known ones (specially in high unknown ratings instead of what you aren't going to like)
- evaluate extrapolate methods, measure success/performance

### Gathering known ratings

- Explicit: ask. Problem: doesn't scale, only a fration of users leave ratings
- Implicit: learn ratings from user actions

### Extrapolating utilities

Problems:
- Matrix utility U is sparse, most people have not rated most items
- *Cold start*: new users haven't history, new items haven't ratings

### Types of recommender systems
- Content-based
- Collaborative based
- Latent factor based

### Content-based systems
Recommend items to customer x similar to previous items rated highly by x

Examples: recommend movies with same director, genre, actor; web articles with similar content; people with many common friends

#### Steps

##### Item profile
For each item, create a profile: a set of features (metadata, tags, set of friends). Think of it as a vector with one
entry per feature (e.g. each actor) with boolean or real value

###### Item profile in docs. TF-IDF
Profile = set of 'important' words in item (document)

How to pick important words?
Use heuristic TF-IDF (Term Frequency - Inverse Document Frequency)

```
TF-IDF calculation

Given a term (item) i and a document j

TFij = fij / (maxk fkj) (normalize to discount doc length effect)

N: total number of documents
ni: number of documents that contain term i

IDFi = log (N/ni)

TF-IDF score:
wij = TFij * IDFi
```

```
Doc profile:
Set of words with highest TF-IDF scores, together with their scores
```

#### User profile
```
The (weighted) average of items rated by user i1,...,in. A variant is normalize weightings using average rating of user
```

More sophisticated aggregations are possible

#### Example 1: Boolean Utility Matrix





## Collaborative

## Evaluation