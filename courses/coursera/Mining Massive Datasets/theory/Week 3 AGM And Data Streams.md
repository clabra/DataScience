Week 3
======

# Affiliation Graph Model (AGM)

AGM is a generative model for graphs using:
- nodes V, Communities C, Memberships M
- each community c has a single probability pc

For each pair of nodes in community A, we connect them with probability Pa.

We can calculate the overall edge probability P(u, v) beween nodes u and v as a function of probabilities Pc (see formula
in course slides):
- c communities shared by nodes u and v
- Think of this as an “OR” function: If at least 1 community says “YES” we create an edge

# BigCLAM
Memberships have strengths

Probability of connection is proportional to the product of strengths (see formula and how to calculate)


# Mining Data Streams

- Ad-hoc and standing queries. Architecture
- One approach: queries are about a window (the last N events)
- What if N doesn't fit in memory ?
- Example: averages
    - What is the average of the integers in the window?
    - For the first N inputs, sum and count to get the average.
    - Afterward, when a new input i arrives, change the average by adding (i - j)/N, where j is the oldest integer in the window.

## Sampling a Stream
Sampling sometimes doesn't work. By instance trying to find the fraction of queries that are unique, asked only once,
sampling 10% of the queries stream.
Our error was sample based on the position in the stream, rather than *sample based on the value of the stream element*.

### Algorithm:
    - hash search queries to 10 buckets, 0...9
    - sample will be the queries that hash to bucket 0
    - this way, all or none of the query instances will be selected
    - therefore the fraction of queries in the sample will be the same than in the whole stream

### Problem: what if the total sample size is limited?

Solution:
Hash to a large number of buckets

Example (fixed sample size):
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














