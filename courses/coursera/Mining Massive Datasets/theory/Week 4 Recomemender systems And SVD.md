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
User profile =  the (weighted) average of item profiles
A variant is normalize weightings using average rating of user
```

More sophisticated aggregations are possible

#### Example 1: Boolean Utility Matrix
[Pending]

Items are movies, only feature is “Actor”
Item profile: vector	with	0	or	1	for	each	Actor

Suppose	user x has	watched	5	movies
2	movies	featuring	actor	A
3	movies	featuring	actor	B
User	profile	=	mean	of	item	profiles
Feature	A’s	weight	=	2/5	=	0.4
Feature	B’s	weight	=	3/5	=	0.6

Same	example,	1-5	star	ratings
Actor	A’s	movies	rated	3	and	5
Actor	B’s	movies	rated	1,	2	and	4

Useful	step:	Normalize	ratings	by	subtracting	 user’s	mean	rating
Actor	A’s	normalized	ratings	=	0,	+2
    Profile	weight	=	(0	+	2)/2	=	1
Actor	B’s	normalized	ra?ngs	=	-2,	-1,	+1
    Profile	weight	=	-2/3


#### Making predictions

```
Cosine similarity:

cosine of angle between the user profile vector and the item proile vector

U(x,i) = cos = (x . i) / (|x| * |i|)
```

#### Pros and Cons
Pros:
- no need for data of other users
- able to recommend to users with unique tastes
- idem new and impopular items
- easy to explain why we are recommending an item

Cons:
- finding the appropiate features is hard (movies, music, images)
- Overspecialization
- Cold-star problem for new users



## Collaborative

Let	rx be the	vector	of	user	x’s	ratings

Let	N	be	the	set	of	k	users	most	similar	to	x who	have	also	rated	item	i

Prediction	for	user	x	and	item	i:
- Option	1:

average of ratings from other similar users

rxi	=	1/k ∑ ryi , y: element of N


- Option	2:

idem, weighting the ratings by similarity

rxi	=	∑ sxy ryi/	∑ sxy
  where sxy	=	sim(x,y)

Similarity can be Jaccard, cosine (simple or with variants)

### Item-item collaborative:
Estimate	rating	for	item	i	based	on	ratings	for	similar	 items
Same prediciton model as in user-user

In	theory,	user-user	and	item-item	are	dual	 approaches
In	practice,	item-item	outperforms	user-user	 in	many	use	cases because items	are	“simpler”	than	users

## Evaluation

Root-mean-square error (RMSE)

((Sum (rxi - rxi*) ^2 ) / N) ^ 1/2

- where	N	=	|T|, number of elements in the test
- rxi is	the	predicted
- rxi* is	the	actual

Problems with error measures:
- narrow on accuracy sometimes means the poin. Consider context, order of prediciotns, etc.
- penalize a method that is good for high ratings (what we care about) and bad for others

## Latent factor models

Netflix challenge

### Multi-scale modeling (deviations)
In practice we get better results with multi-scale modeling of the data: combine top level, “regional” modeling of the
data, with a refined, local view:

 - Global:
 Overall deviations of users/movies

```
In practice we get better estimates if we model global deviations of users/models:

rxi	=	bxi + ∑ sxy ryi/	∑ sxy

- bxi = mu + bx + bi
- mu  = overall mean rating
- bx  = rating deviation of user x
    = (avg. rating of user x) – mu
- bi  = (avg. rating of movie i) – mu
```

-  Factorization:
 Addressing “regional” effects

-  Collaborative filtering:
 Extract local patterns

#### Example:

- Global:

    - Mean movie rating: 3.7 stars
    -  The Sixth Sense is 0.5 stars above avg.
    -  Joe rates 0.2 stars below avg.

Baseline estimation:
Joe will rate The Sixth Sense 4 stars

- Local neighborhood (CF/NN):

    - Joe didn’t like related movie Signs

Final estimate:
Joe will rate The Sixth Sense 3.8 stars


