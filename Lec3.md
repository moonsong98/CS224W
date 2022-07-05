# Lecture 3. Node Embeddings

## Motivation

Unlike traditional graph ML, graph representation learning is free from feature engineering.
It extracts features of nodes from the given graph without labels and features,
and it's task independent. It works based on **Embedding**.

## Node Embedding

### Goal for Node Embedding

Encode nodes so that similarity in the embedding space(usually dot product is used)
approximates similarity in original graph.  

$ENC(u)=z_{u}, similarity(u, v) \approx z_{v}^{T}z_{u}$

---

## Approach 1. Shallow Encoding

Shallow Encoding is a simplest encoding approach. The matrix
$\mathbb{Z} \in \mathbb{R}^{d\cdot |V|}$ that
we learn has columns of node embeddings,
and $v\in \mathbb{I}$
is composed of zeros except one 1 which indicates node $v$.  

$ENC(v)=z_{v}=\mathbb{Z} \cdot v$

### Similarity Function

1. Are Linked?
2. $\#$ of sharing Neighbors
3. Similairity of structural role  
$\vdots$

---

## Approach 2. Random Walk

Random walk is a path along iterations of the following sequence:
From current node, randomly choose a neighbor node and move to it.

### Idea

Make $z_{u}^{T}z_{v} \approx P(\textrm{u and v
co-occur on a random walk over the graph})$

### Pros

1. Expressivity: Random walk is a flexible stochastic definition of node similarity
that incorporates both local and higher-order neighborhood information
2. Efficienty: Only need to consider pairs that co-occur on random walks

### Feature Learning as Optimization

#### Goal

Learn a function $f: u\rightarrow \mathbb{R}^{d}, f(u)=z_{u}$,
so that nearby nodes are close together in the network.

+ $N_{R}(u)$: Multiset of nodes visited on random walks starting from u
+ $P(v|z_{u})$:
Probability of visiting node $v$ on random walks starting from node u

+ Log-likelihood objective: $\max_{f}\sum_{u\in V}{\log P(N_{R}(u)|z_{u})}$

#### Random Walk Optimization

1. Run short fixed-length random walks starting from each node u in the graph
using some random walk strategy.
2. For each node u collect $N_{R}(u)$, the multise of nodes
visited on random walks starting from u.
3. Optimize objective function using $N_{R}(u)$.

Objective Function below is equivalent as above.
$\mathcal{L}=\sum_{u\in{V}}{\sum_{v\in{N_{R}(u)}}{-\log(P(v|z_{u}))}}$  
s.t. $P(v|z_{u})=\frac{\exp(z_{u}^{T}z_{v})}{\sum_{n\in V}{\exp(z_{u}^{T}z_{n})}}$

Problem: Since time complexity of calculating denominator takes $O(|V|^2)$,
it too expensive.  
Solution: Use Negative Sampling!

#### Negative Sampling

$\log(\frac{\exp(z_{u}^{T}z_{v})}{\sum_{n\in V}{\exp(z_{u}^{T}z_{n})}}) \approx
\log(\sigma(z_{u}^{T}z_{n_{i}})), n_{i} \backsim P_{V}$

$P_{V}(v)$ is proportional to its degree, and should sample nodes not on the walk.

### Strategies

#### DeepWalk

Fixed-length, unbiased random walks starting from each node(Which is described above).

#### Node2Vec

Flexible, biased random walks that can trade off between
local and global views of the network.

##### Strategies of Node2Vec

+ BFS
+ DFS
+ Return back to the previous node

Return parameter $p$: return bak to the previous node  
In-out parameter $q$: ratio of BFS vs. DFS

##### Algorithm

1) Compute random walk probabilities
2) Simulate $r$ random walks of length $l$ strating fomr each node $u$
3) Optimize the node2vec objective using Stochastic Gradient Descent

All 3 steps are individually parallelizable, so it has linear-time complexity.

## Graph Embedding

### Goal for Graph Embedding

Embed a subgraph or an entire graph G.

### Approach 1. Simple Idea

Sum up node embeddings

### Approach 2. Using a Virtual Node

Introduce a virtual node to embed the graph

### Approach 3. Anonymous Walk Embeddings

Simulate anonymous walks $w_{i}$ of $l$ steps and record the path with numbers
that is granted as they first exist.  
The maximize the probability to predict the next walk from given walks.
