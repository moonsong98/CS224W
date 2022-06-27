# Traditional ML

Traditional ML uses hand-crafted features. 

## Node-Level Features
#### Goal
Characterize the structure and position of a node in the graph, using following features.
+ Node Degree
+ Node Centrality
+ Clustering Coefficient
+ Graphlets

---
#### Node Degree
$k_u=$ # of neighboring nodes of $u$ <br />
Drawback: Doen't care about node importance

---
#### Node Centrality
##### Eigenvector Centrality
$C_u=\frac{1}\lambda\sum_{v\in{N(v)}}C_v$ &rarr; $\lambda{c}=Ac$ <br />
By Perron-Frobenius Theorem, if A is a non-negative irreducible matrix, $\lambda_{max}$ is always positive and unique. $c_{max}$ corresponding to $\lambda_{max}$ is used for eigenvector Centrality
##### Betweeness Centrality
$c_v=\frac{\textrm{\# of shortest paths between s and t that contains v}}{\textrm{\# of shortest paths between s and t}}$
##### Closeness Centrality
$c_v=\frac{1}{\sum_{u\neq v}{\textrm{shortest path length between u and v}}}$

---
#### Clustering Coefficient
$e_v=\frac{\textrm{\# of edges among neighboring nodes}}{k_v \choose 2}\in[0, 1]$ <br />
It counts the number of triangles in the ego-network

---
#### Graphlets - Generalized Version of Clustering Coefficient and Node Degree
##### Graphlet 
Rooted connected induced non-isomorphic subgraphs<br />
+ Rooted: Graphlet id (Root/position of nodes), position of the node is considered
+ Induced subgraph: It is a subgraph formed from a subset of vertices and all of edges connecting the vertices
+ isomorphic: Same number of nodes are connected in the same way 
##### GDV(Graphlet Degree Vector)
v[i]: # of i<sup>th</sup> graphlet in a graph

---
#### Function of Features
##### Importance Based Features
+ It captures the importance of a node in a graph <br /> 
+ Use for Prediction Influential Nodes in a raph <br />
  ex) Predicting influential users in a social network
##### Structure Based Features
+ It captures the topological properties of local neighborhood around a node
+ Use for Predicting particular role of a node plays in a network

<br /> <br/> <br/>


## Link-Level Prediction
### Goal
Predict new links based on the existing links by a score computation.
During test time, new edges which are not in a graph are ranked in decreasing order, and top K edges are predicted.
### Methodlogy
1. Links missing at random <br />
   Remove a random set of edges and the aim to predict them
2. Links over time <br />
   Given edges up to t<sub>0</sub>, predict new edges that would come up until t<sub>1</sub> <br/>

Evaluation works by counting the number of right predictions.

## Link-Level Features
#### Distance-based Feature
Shortest path between two nodes. <br />
Drawback: Does not capture the degree of neighborhood overlap(or strength of connection)
#### Local Neighborhood Overlap

1. Common Neighbors: $|N(u_1)\cup N(u_2)|$

2. Jaccard's Coefficient: $\frac{|N(u_1)\cap N(u_2)|}{|N(u_1)\cup N(u_2)|}$

3. Adamic-Adar Index: $\sum_{u\in N(u_1)\cap N(u_2)}{\frac{1}{log(k_u)}}$

Drawback: If two nodes doesn't have common neighbors, they cannot not be connected.
#### Global Neighborhood Overlap - Kaltz Index
Count the number of paths of all lengths between a pair of nodes. <br />
Let A an adjacency matrix. Then A<sup>k</sup><sub>ij</sub>  is number of paths of length len between u<sub>i</sub> and u<sub>j</sub>. <br />

$S_{v_1v_2}=\sum_{l=1}^{\infty}{\beta^{l}A^{l}_{v1v2}}$ <br />

$S=sum_{i=1}^{\infty}{B^iA^i}=(I-\beta A)^{-1}-I$ <br />

<br /> <br/> <br/>

## Graph-Level Prediction
### Goal
Get features that characterize the structure of an entire graph
### Kernel
+ Instead of feature vector, we use kernel to represent the graph
+ K(G, G`) $\in \mathbb{R}$ measures similarity between two graphs
+ Kernel matrix must always be PSD
+ There exists a feature representation ${\phi(\cdot)}$ such that K(G, G`) = ${\phi(G)^T\phi(\grave{G})}$

#### Grpahlet Kernel
Different graphlet as discussed in node feature
- Nodes doesn't need to be connected
- The graphlets are not rooted

$g_k=(g_1, g_2,...,g_k)$: list of graphlets of size k <br />
Let $f_G$ a graphlet kernel. It is usually used by normalized version $h_G=\frac{f_g}{\sum{\textrm{entries of }f_g}}$ </br> 

Drawback: Subgraph isomorphism test is NP-Hard

#### Weisfeiler-Lehman Kernel
It is a generalized version of "Bag of Node Degrees". <br/>
Iteratively refine node colors by <br/>
$c^{(k+1)}=f({c^{(k)}(v), \{c^{(k)}(u)}\}_{u \in N(v)})$ <br/>
f is a hash function, and super cript k means k<sup>th</sup> iteration. <br/> 
Finally count the number of nodes that has colored by each color, and make it a vector. </br>
Each vector is a WL kernel. </br>
Since each iteration takes O(|E|), total time complexity is O(K|E|)
