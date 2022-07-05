# Lecture 4. Link Analysis: PageRank

## Graph Representation by Matrix

### Pros

1. Can determine node importance via random walk.
2. Can obtain node embeding via matrix factorization.

## Page Rank

All pages are static pages. So, hyperlink is the only way to navigate between pages.

### Matrix Formulation

$d_{u}$: A number of outdegrees of node u.  

#### Stochastic Adjacency marix $M$

$M_{vu}=\frac{1}{d_{u}}$ if $u\rightarrow{v}$.  

#### Rank Vector $r$

$r_{v}=\sum_{{u}\rightarrow{v}}{\frac{r_{u}}{d_{u}}}$, sum of entries is 1.  
Out Edges of vector u have same weight, $\frac{r_{u}}{d_{u}}$  
Rank of node u is determined by sum of weights of incoming edges.

### Random Walk Assumption

Assume that at the beginning, there is a initial rank.  
At time $t$, surfer is in page $u$. And at time $t+1$,
surfer would move to connected page $v$ with probability of $\frac{1}{d_{u}}$.  
Let's say, $u_{th}$ entry of $p(t)$ is a probability
that surfer would be at page $u$ at time $t$.  
After infinite time past, $p(t+1)=M\cdot{p}(t)=p(t)$,
which means that $p(t)$ becomes a stationary distribution.

### Flow Equation & Eigenvector Centrality

$1\cdot{r}=M\cdot{r}$  
Starting from any vector $u$,
the limit of $M(M(...M(Mu)))$ is $r$ by limiting distribution.  
Since $M$ is a markov matrix, it's largest eigenvalue is $1$.  
So, $r$ is the principal eigenvector of $M$ and it is a eigenvector centrality.

### How to get $r$

Since the size of matrix is large, solving charateristic equation is inefficient.  
Instead, use an iterative procedure.  

1) Assign each node an initial page rank (e.g. $[{\frac{1}{N}...\frac{1}{N}}]$)
2) Repeat untile convergence s.t.

$\sum_{i}|r_{i}^{t+1}-r_{i}^{t}|<\epsilon$  

In practice, 50 times is enough.

### Two Problems and Solutions

#### Dead Ends

Some nodes are sinks which have no out link.  
This violates the mathmetical assumption, that sum of entries of each node is 1.

##### Solution to Dead Ends

Assign 1/N for corresponding entries of nodes which are dead ends.

#### Spider Trap

All out links are within the Group

##### Solution to Spider Trap

+ Probability $\beta$: Follow a link at Random
+ Probability $1-\beta$: Jump to a random Page

Common $\beta$ is $[0.8, 0,9]$

### Google Matrix

$G = \beta \cdot {M}+(1 - \beta) [ \frac {1} {N} ] _ {N * N}$

## Recommendation

+ Given: A bipartite graph representing user&item interaction.  
+ Goal: Anticipate future connection of users and items.  
Q) What items should be recommended to a user who interacts with item(s) $Q$?

### Intuition

If items $q$ & $p$ are interacted by similar users,
recommend $p$ to user who interacts to $q$.  

### Personalized Page Rank

Let Q is a set of items that we are interested in.  
Starting form some nodes in Q, we do a random walk process with some probability
to jump back to nodes in Q.  
This is a modification of page rank algorithm, that the probability to jump back
to some node doesn't need to be same.  
If $|Q|=1$, we call it a **Random Walk with Restarts**.

## Node Embedding by MF

We can obtain node embedding by MF.  
Let's think of the simplest similarity
such that node u and v are similar if they are connected.  
$Z \in \mathbb{R} ^ {d * N}$ in an encoder as an embedding lookup.  

Objective: $\min_{Z} {||A-Z^{T}Z||}_ {2}$

DeepWalk and node2vec are also existing.

### Limitations

1. Cannot obtain embeddings for nodes not in the training set.
2. Cannot capture structural similarity.
3. Cannot utilize node, edge and graph features.
