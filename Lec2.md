# Traditional ML

Traditional ML uses hand-crafted features.

## Node-Level Features

### Goal of Node-Level Features

Characterize the structure and position of a node in the graph, using following features.

+ Node Degree
+ Node Centrality
+ Clustering Coefficient
+ Graphlets

---

### Node Degree

$k_{u}=$ # of neighboring nodes of $u$  
Drawback: Doen't care about node importance

---

### Node Centrality

#### Eigenvector Centrality

$C_{u}=\frac{1}\lambda\sum_{v\in{N(v)}}C_{v} \rightarrow \lambda{c}=Ac$  
By Perron-Frobenius Theorem, if A is a non-negative irreducible matrix,
$\lambda_{max}$ is always positive and unique.
$c_{max}$ corresponding to $\lambda_{max}$ is used for eigenvector Centrality

#### Betweeness Centrality

$c_{v}=\frac{\textrm{\# of shortest paths between s and t
that contains v}}{\textrm{\# of shortest paths between s and t}}$

#### Closeness Centrality

$c_{v}=\frac{1}{\sum_{u\neq v}{\textrm{shortest path length between u and v}}}$

---

### Clustering Coefficient

$e_{v}=\frac{\textrm{\# of edges among neighboring nodes}}
{k_{v} \choose 2}\in[0, 1]$  
It counts the number of triangles in the ego-network.

---

### Graphlets - Generalized Version of Clustering Coefficient and Node Degree

#### Graphlet

Rooted connected induced non-isomorphic subgraphs  

+ Rooted: Graphlet id (Root/position of nodes), position of the node is considered
+ Induced subgraph: It is a subgraph formed from
a subset of vertices and all of edges connecting the vertices
+ isomorphic: Same number of nodes are connected in the same way

#### GDV(Graphlet Degree Vector)

v[i]: \# of $i_{th}$ graphlet in a graph

---

### Function of Features

#### Importance Based Features

+ It captures the importance of a node in a graph
+ Use for Prediction Influential Nodes in a graph  
  ex) Predicting influential users in a social network

#### Structure Based Features

+ It captures the topological properties of local neighborhood around a node
+ Use for Predicting particular role of a node plays in a network

## Link-Level Prediction

### Goal of Link-Level Prediction

Predict new links based on the existing links by a score computation.
During test time, new edges which are not in a graph
are ranked in decreasing order, and top K edges are predicted.

### Methodlogy

1. Links missing at random
   Remove a random set of edges and the aim to predict them
2. Links over time
   Given edges up to $t_{0}$, predict new edges that would come up until $t_{1}$

Evaluation works by counting the number of right predictions.

## Link-Level Features

### Distance-based Feature

Shortest path between two nodes.  
Drawback: Does not capture the degree of neighborhood overlap(or strength of connection)

### Local Neighborhood Overlap

1. Common Neighbors: $|N(u_{1})\cup N(u_{2})|$

2. Jaccard's Coefficient: $\frac{|N(u_{1})\cap N(u_{2})|}{|N(u_{1})\cup N(u_{2})|}$

3. Adamic-Adar Index: $\sum_{u\in N(u_{1})\cap N(u_{2})}{\frac{1}{log(k_{u})}}$

Drawback: If two nodes doesn't have common neighbors, they cannot not be connected.

### Global Neighborhood Overlap - Kaltz Index

Count the number of paths of all lengths between a pair of nodes.  
Let A an adjacency matrix. Then $A ^ k_{ij}$  is number of paths of length
between $u_{i}$ and $u_{j}$.  

$S_{v_{1}v_{2}}=\sum_{l=1}^{\infty}{\beta^{l}A^{l}_ {v1v2}}$

$S=sum_{i=1}^{\infty}{B^iA^i}=(I-\beta A)^{-1}-I$  

## Graph-Level Prediction

### Goal of Graph-Level Prediciton

Get features that characterize the structure of an entire graph

### Kernel

+ Instead of feature vector, we use kernel to represent the graph
+ K(G, G`) $\in \mathbb{R}$ measures similarity between two graphs
+ Kernel matrix must always be PSD
+ There exists a feature representation ${\phi(\cdot)}$ such that K(G, G`) = ${\phi(G)^T\phi(\grave{G})}$

#### Grpahlet Kernel

Different graphlet as discussed in node feature

+ Nodes doesn't need to be connected
+ The graphlets are not rooted

$g_{k}=(g_{1}, g_{2},...,g_{k})$: list of graphlets of size k  
Let $f_{G}$ a graphlet kernel. It is usually used by normalized version
$h_{G}=\frac{f_{g}}{\sum{\textrm{entries of }f_{g}}}$  

Drawback: Subgraph isomorphism test is NP-Hard

#### Weisfeiler-Lehman Kernel

It is a generalized version of "Bag of Node Degrees".  
Iteratively refine node colors by  
$c^{(k+1)}=f({c^{(k)}(v), \{c^{(k)}(u)}\}_{u \in N(v)})$  
f is a hash function, and superscript k means $k_{th}$ iteration.  
Finally count the number of nodes
that has colored by each color, and make it a vector.  
Each vector is a WL kernel.  
Since each iteration takes O(|E|), total time complexity is O(K|E|)
