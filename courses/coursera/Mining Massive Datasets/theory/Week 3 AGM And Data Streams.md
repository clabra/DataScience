Week 3
======

# Affiliation Graph Model (AGM)

AGM is a generative model for graphs using:
- nodes V, Communities C, Memberships M
- each community c has a single probability Pc

For each pair of nodes in community A, we connect them with probability Pa.

```
We can calculate the overall edge probability P(u, v) beween nodes u and v as a function of probabilities Pc (see formula
in course slides):
- c communities shared by nodes u and v
- Think of this as an “OR” function: If at least 1 community says “YES” we create an edge
```

# BigCLAM
Memberships have strengths

```
Probability of connection is proportional to the product of strengths (see formula and how to calculate)
```


# Mining Data Streams

- Ad-hoc and standing queries. See architecture diagram in course slides
- One approach: queries are asked about a window (the last N events)
- Problem: what if N doesn't fit in memory?. We'll have to sample input

There are some queries over streams that are very ease to implement
Example: averages
    - What is the average of the integers in the window?
    - For the first N inputs, sum and count to get the average.
    - Afterward, when a new input i arrives, change the average by adding (i - j)/N, where j is the oldest integer in the window.

But for another queries we have to apply next techniques.

## Bloom Filters

Example: Suppose we have a web crawler. It maintains a central store with the URL's to parse. Parse tasks are parallelized
receiving URL as input and output URL found in crawled page. How to filter repeated URLs?.

```
A Bloom filter is an array of bits together with a number of hash functions.

All positions in array are set to 0 initially

Each hash function receives an element of the stream as input and it returns a position in the array.

We set this position (positions start form 0) from 0 to 1. If the position is already set to 1, this means this element
is repeated.

```

### Performance of Bloom filters

```
Probability of a false positive:
(fraction of 1's) ^ (# of hash functions)

The number of 1’s is approximately:
the number of elements inserted x the number of hash functions.

 But collisions lower that number slightly.
```

#### Throw darts at a target
Turning random bits from 0 to 1 is like throwing d darts at t targets, at random.  How many targets are hit by at least one dart
Probability a given target is hit by a given dart = 1/t.

```
Probability none of d darts hit a given t target is:
(1-1/t)^d
rewrite as (1-1/t) ^ t(d/t) ~= e ^ (-d/t)
```


#### Example
Suppose we use an array of 1 billion bits, 5 hash functions, and we insert 100 million elements.

t = 10^9
d = 10^8 * 5

Fraction of 0's:
e ^ (-1/2)

density of 1's:
1 - e ^ (-1/2)

Probability of a false positive (note this filter is never going to give a false negative):
(1 - e ^ (-1/2)) ^ (5)


#### Question 1
Suppose we hash the elements of a set S having 20 members, to a bit array of length 99. The array is initially all-0's,
and we set a bit to 1 whenever a member of S hashes to it. The hash function is random and uniform in its distribution.
What is the expected fraction of 0's in the array after hashing? What is the expected fraction of 1's? You may assume
that 99 is large enough that asymptotic limits are reached.

	- The fraction of 0's is 1-e-79/99.
	- The fraction of 1's is e-79/99.
    - The fraction of 1's is 1-e-20/99.
	- The fraction of 0's is e-79/99.


## Sampling a Stream

Sampling sometimes doesn't work.

Example:
Trying to find the fraction of queries that are unique, asked only once,
sampling 10% of the queries stream.
Our error was to sample based on the position in the stream, rather than

```
    sample based on the value of the stream element
```

### Algorithm:
    - hash search queries to 10 buckets, 0...9
    - sample will be the queries that hash to bucket 0
    - this way, all or none of the query instances will be selected
    - therefore the fraction of queries in the sample will be the same than in the whole stream

```
Problem: what if the total sample size is limited?

Solution:
Hash to a large number of buckets
```

#### Example (fixed sample size):
- We want to limit the size in our 10% search-query sample.
- Hash to, say, 100 buckets, 0...99
- Take for the sample those elements hashing to buckets 0 to 9
- If the sample gets too big, get ride of bucket 9. Still too big, get ride of bucket 8...

### Sampling key-value pairs
Our technique generalizes to any form of data that we can see as Key Value tuples (K,V)
Distinction: we want *our sample be based on keys only*, not pairs. In the search-query example the data was "all key".

Example (salary ranges):
- data = tuples (EmpId, Dept, Salary)
- query: what is the average range of salaries within a department?
- key: Dept, Value = (EmpId, Salary)
- sample picks some departments, take salaries for all employees of that department


### Question 2
A certain Web mail service (like gmail, e.g.) has 10^8 users, and wishes to create a sample of data about these users,
occupying 10^10 bytes. Activity at the service can be viewed as a stream of elements, each of which is an email.
The element contains the ID of the sender, which must be one of the 10^8 users of the service, and other information,
e.g., the recipient(s), and contents of the message. The plan is to pick a subset of the users and collect in the 10^10
bytes records of length 100 bytes about every email sent by the users in the selected set (and nothing about other users).
The method of Section 4.2.4 will be used. User ID's will be hashed to a bucket number, from 0 to 999,999. At all times,
there will be a threshold t such that the 100-byte records for all the users whose ID's hash to t or less will be retained,
and other users' records will not be retained. You may assume that each user generates emails at exactly the same rate
as other users. As a function of n, the number of emails in the stream so far, what should the threshold t be in order
that the selected records will not exceed the 10^10 bytes available to store records? From the list below, identify the
true statement about a value of n and its value of t.

Explanation:
Suppose that the fraction of users in the sample is p. That is, 108p is the number of users whose records are stored.
Since each user generates 10-8 of the emails in the stream, when n emails have been seen, the number of records stored
is 108p10-8n = pn. Note that is number does not depend on the number of users of the service.
Since each record is 100 bytes, we can store 1010/100 = 108 records. That is, pn = 108, or p = 108/n. If the threshold is t,
the fraction p of users that will be in the selected set is (t+1)/1,000,000.




