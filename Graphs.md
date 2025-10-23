# Graphs

## Graph Definitions
A graph is a set of nodes or vertices with edges between some of them. Let $V$ be the set of vertices and $E$ be the set of edges.
nodes with an edge in between them are called neighbors. The degree of a vertex is the number of its neighbors. This completes the
definition of an undirected graph. 

A directed graph is in which each edge has a direction, and nodes have out-neighbors and in-beighbors, the numbers of
which are respectively called outdegree and indegree. The maximum number of children of a node is called its arity. All nodes can be mutual out or in-neighbors. 

Weighted graphs are those whose edges have
values, called costs or lengths. 
## Data Structures
Different representations of graphs merit the applications of different search algorithms. Consider three common implementations of graphs
### Adjacency Lists
Edges can be represented using adjacency vertex lists or adjacency edge lists. An adjacency vertex list for a vertex $i$ contains 
the vertices that are adjacent to $i$. Adjacency edge list for a vertex $i$ contains the edges adjacent to vertex $i$. 
We define an array of lists, with $n$ entries each representing a list. For example,
### Adjacency Matrix
Still representing vertices by their indices, the adj matrix of an iunweighted graph is structured as a square `int[][]` where `int[0][]` represents the row containing an array of binary indicators
suggesting whether each of the other vertices are immediately adjacent in the sense there is an edge connecting to the vertex 0. For example, `int[0][3]=1` means vertex 4 is a neighbour of vertex 0. For a weighted graph, 
we may replace the binary indicator with edge weights.
```
// Vertices: A, B, C, D
// Edges: A→B, A→C, B→D, C→A

int[][] adjMatrix = {
    // A  B  C  D
    { 0, 1, 1, 0 },  // A
    { 0, 0, 0, 1 },  // B
    { 1, 0, 0, 0 },  // C
    { 0, 0, 0, 0 }   // D
};

// Interpretation:
// adjMatrix[i][j] == 1 means there's an edge from vertex i to vertex j

```
For weighted graphs, we use infinity to represent the non-existence of an edge in between disconnected vertices
```
// Weighted directed graph
// Edge weights: A→B = 4, A→C = 2, C→B = 1, B→D = 3, C→D = 5

int INF = Integer.MAX_VALUE; // represents no edge
int[][] weightedMatrix = {
    // A     B     C     D
    { 0,    4,    2,    INF }, // A
    { INF,  0,    INF,  3   }, // B
    { INF,  1,    0,    5   }, // C
    { INF,  INF,  INF,  0   }  // D
};

// Interpretation:
// weightedMatrix[i][j] gives the edge cost from i to j.
// Use INF if there’s no direct edge.
```

Which one is better for what purpose? if the graph is dense with many edges, using an adjacency matrix is 
preferred. If the graph is sparse with a few edges, adjacency lists are better. 
### Map(Vertex -> Neighbour)
This implementation merits the backtracking of paths. 

For example, one may record a key value pair as (v1, v2)=(to, from) meaning we went to
$v2$ from $v1$. Then upon reaching a destination $vi$ we can backtrack the path by calling `vj=map.get(vi)`, then recursively calling `map.get(vj)` until 
we return to the starting point. 
## BFS & DFS & Dijkstra
Consider a graph with $V$ vertices and $E$ edges

| **Algorithm** | **BFS** | **DFS** | **Dijkstra’s** |
|-------------|--------------------------------|------------------------------|---------------------------|
| **Goal** | Explore all vertices in one adjacency level distanced from the starting node before advancing to the next level | Explores as deep as possible along each branch before backtracking | Finds the minimum-cost path from a source to all other nodes |
| **Graph Type** | unweighted graphs | Unweighted graphs | Weighted graphs (non-negative weights) |
| **Data Structure Used** | Queue (First In First Out) | Stack (Last In First Out) | Priority Queue (Min-Heap) |
| **Guaranteed to find the shortest path?** | Yes, for unweighted graphs | No | Yes, for graphs with non-negative weights |
| **Time Complexity** | O(V + E) walks every edge and vertex once | O(V + E) | O((V + E) log V) (see footnote (1)) with a priority queue |
| **Space Complexity** | O(V) | O(V) | O(V + E) |
| **The Invariant** | Enqueue -> Dequeue -> Process -> Enqueue Children; repeat | Push -> Pop -> Process -> Push Children; repeat | Add -> Poll (get min by distance from minHeap) -> update distance for children; Repeat (see note (2)) |
| **Typical Use Cases** | Shortest path in unweighted graphs (returns the paths with least number of nodes), finding connected components | Detecting cycles, path existence | Shortest path in weighted networks |

_(1) Why does Dijkstra's take O((V + E) log V) runtime? It performs $V$ extractions of the minimum element and $E$ updates, each taking $O(logV)$ time due to the structure of the
minHeap. In a minHeap the extraction of the minimum element terminates when one reaches the leaf level. Thus the number of levels is n for $V/2^n = 1 \iff n = log_2 V$_. 

_(2) Why are we guaranteed the Dijkstra's actually terminate upon identifying the shortest distances? For example, can we potentially waste time comparing the smallest distance node to 
all other larger distance nodes to actually conclude it is indeed the shortest path? No. Extracting from the heap automatically gives us the vertex with the smallest tentative distance. 
So we never compare against larger distances explicitly — the heap ensures we only ever deal with the smallest current value. Once we extract a vertex from the heap, and mark it as “visited” or “finalized”. 
Any later discovery of a “shorter path” to that vertex is ignored, because we know such a path cannot exist (non-negative weights)._

Illustrating examples: 
```
// BFS 

---------------------------------------
Step 1: Visit A
   A*
  /|\
 B-C-D
  \|/
   E
---------------------------------------
Step 2: Visit B, C, D (neighbors of A)
   A
  /|\
 B*-C*-D*
  \|/
   E
---------------------------------------
Step 3: Visit E (neighbor of B, C, D)
   A
  /|\
 B-C-D
  \|/
   E*
---------------------------------------
Order: A -> B -> C -> D -> E
---------------------------------------
// DFS
---------------------------------------
Step 1: Visit A
   A*
  /|\
 B-C-D
  \|/
   E
---------------------------------------
Step 2: Visit B (first neighbor of A)
   A
  /|\
 B*-C-D
  \|/
   E
---------------------------------------
Step 3: Visit C (first unvisited neighbor of B)
   A
  /|\
 B-C*-D
  \|/
   E
---------------------------------------
Step 4: Visit D (first unvisited neighbor of C)
   A
  /|\
 B-C-D*
  \|/
   E
---------------------------------------
Step 5: Visit E (first unvisited neighbor of D)
   A
  /|\
 B-C-D
  \|/
   E*
---------------------------------------
Order: A -> B -> C -> D -> E
---------------------------------------

//Dijkstra (starting at A)
Initial graph (weights in parentheses):

       A(0)
     /  |  \
 (1)/  (2)  (5)
   B    C    D
    \   |   /
   (3) (1) (4)
      \ | /
        E
       (6)

---------------------------------------
Step 1: Start at A
---------------------------------------
Distances: A=0, B=1, C=2, D=5, E=∞  
Visited: [A]

---------------------------------------
Step 2: Visit B (1)
---------------------------------------
Via B:
  C = min(2, 1+2) = 2  
  E = min(∞, 1+3) = 4  
Visited: [A, B]

---------------------------------------
Step 3: Visit C (2)
---------------------------------------
Via C:
  D = min(5, 2+3) = 5  
  E = min(4, 2+1) = 3  
Visited: [A, B, C]

---------------------------------------
Step 4: Visit E (3)
---------------------------------------
Via E:
  D = min(5, 3+4) = 5 (unchanged)
Visited: [A, B, C, E]

---------------------------------------
Step 5: Visit D (5)
---------------------------------------
Done.

---------------------------------------
Final shortest distances:
---------------------------------------
A:0  
B:1  
C:2  
E:3  
D:5

---------------------------------------
Exploration order:
---------------------------------------

        A(0)
      /  |  \
   B(1) C(2) D(5)
      \  |  /
        E(3)

```
For graph traversals there are in general three steps. 
1. Initialization: usually involves (a) A list of visited nodes (b) a set of nodes to be visited (in deque or stack) (c) path traversed thus far
2. Exploration of neighbours
3. Process neighbour
## DFS
### General Traversal
Maintains two dynamic records: (a) for each node, whether visited it or not (an array of boolean); 
(b) an array of nodes to be visited, initially empty.

PseudoCode
```

```
### Finding Cycles

## BFS
PseudoCode
```
while q not empty
    (v, State) = q.pop()
    if v == goal
        return true
    for each edge e of v
        u = other endpoint
        if not constraint traversable(e, (u, v)) continue
        nextState = State
        if stateUpdate at v: nextState.add(stateUpdate)
        if u not visited
            mark u visited 
            q.push(v, nextState)
```
## Dijkstra's 
**(Dijkstra's)** Given a directed graph $G = (V, E)$, with NONNEGATIVE edge weights, the single-source shortest path problem
can be solved using Dijkstra's algorithm in time $O(mlogn)$ where $|V| = n$ and $|E|=m$, if implemented using a heap data structure.
(See "/src/Heap/HeapBasics.java" on heap structure)
