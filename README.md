# Approximate_Nearest_Neighbors
Program that uses LSH and HyperCube method to find nearest neighbors


Locality Sensitive Hashing (LSH)

Locality Sensitive Hashing is an algorithmic technique that hashes similar input items into the same buckets with high probability. The layers in which this algorithm is implemented are the following:

1) Create k h functions. Each of every one has 4 parts
  
  vector p: The vector that we want to store
  vector v: A vector of d-dimensions, same as the input vectors. Each value is generated normally at the start of execution.
  double t: Also generated at the start of execution but uniformly.
  int w: "window". This is set as 650 in LSH/hash.cpp, line 21. Feel free to experiment.

  h(p) = floor ((p*v + t)/w))


2)AmplifiedHashFunction: This function is called an amplified hash function as it "combines" k HashFunction instances to form a new (amplified) hash function.
Specifically, on creation, k HashFunction objects are initialized. When an item needs to be hashed, it gets hashed by all k of those hash functions, in the end, the results get concatenated bitwise to produce the "final" hash.

g(p) =  [(r1h1(p) + r2h2(p) + · · · + rkhk (p)) mod M] mod TableSize where r is a random vector


3)LSH: It contains L hash tables (so L AmplifieadHashFunctions) and whenever an item is inserted, it is added for each hash table into its respective bucket.
To summarize, when LSH is initialized, we have L hash tables where each one contains one instance of each of our items. Whenever a query item arrives, we hash it for every hash table and search for its nearest neighbors among the L buckets (one from each hash table).

Querying trick
For every p, store
ID(p) = r1h1(p) + r2h2(p) + · · · + rkhk (p) mod M.
Then indexing hash-function is g(p) = ID(p) mod TableSize.
ID is locality sensitive: depends on w-length cells on the v-lines.
Avoid computing Euclidean distance to all elements p in bucket; do it only for p: ID(p) =ID(q)


Projection in Hypercube
Randomized projection into Hypercube is a similar algorithmic technique to LSH. However, instead of L hash tables with their own AmplifiedHashFunction, our dataset is stored into the vertices of a Hypercube. Layers:

1)HashFunction: This is the same class implemented in LSH, whenever an item comes as input, it is shifted and then the "h" formula is calculated on it.


2)f: This function has a relatively simple work to do. All it has to do is map the output from HashFunction to {0,1}. To ensure that the values are distributed uniformly, a random integer among the range of HashFunction values is selected. Once selected, whenever a HashFunction value is inserted, if the random value is greater than the "h" value, it returns 0. Else, it returns 1.


3)Hypercube: This is the final layer. A hypercube of dimension d' is created alongside d' HashFunctions and f. This hypercube is represented as an array of size 2d'. Whenever an item is inserted, it is first hashed by each HashFunction and then mapped to {0,1} by f. The result is a binary number of length d' (which is equivalent to a decimal number i.e. the index of the vertex in which the item will be inserted). The process is similar whenever a query item arrives, as it is hashed, then mapped. Once mapped, we have a starting vertex from which we want to start our search for near neighbors. However, we are not limited to only this vertex, it is possible to traverse nearby hypercube vertices (e.g. vertices with hamming distance of 1 from the starting vertex).
