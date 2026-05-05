# 🔗 Graph Algorithm Patterns — From 1 to N

> A curated, structured guide to mastering **Graph Algorithms** — from absolute basics to advanced competitive-level problems.
> Every pattern has a concept explanation, visual intuition, JavaScript template, and hand-picked LeetCode problems.

---

## 📌 Table of Contents

| # | Pattern | Difficulty |
|---|---------|------------|
| 1 | [Graph Representation](#1-graph-representation) | 🟢 Beginner |
| 2 | [Breadth First Search (BFS)](#2-breadth-first-search-bfs) | 🟢 Beginner |
| 3 | [Depth First Search (DFS)](#3-depth-first-search-dfs) | 🟢 Beginner |
| 4 | [Cycle Detection](#4-cycle-detection) | 🟡 Intermediate |
| 5 | [Topological Sort](#5-topological-sort) | 🟡 Intermediate |
| 6 | [Connected Components & Union-Find (DSU)](#6-connected-components--union-find-dsu) | 🟡 Intermediate |
| 7 | [Bipartite Graph Check](#7-bipartite-graph-check) | 🟡 Intermediate |
| 8 | [Shortest Path — Dijkstra's Algorithm](#8-shortest-path--dijkstras-algorithm) | 🟠 Advanced |
| 9 | [Shortest Path — Bellman-Ford & SPFA](#9-shortest-path--bellman-ford--spfa) | 🟠 Advanced |
| 10 | [Shortest Path — Floyd-Warshall (All Pairs)](#10-shortest-path--floyd-warshall-all-pairs) | 🟠 Advanced |
| 11 | [Minimum Spanning Tree — Kruskal & Prim](#11-minimum-spanning-tree--kruskal--prim) | 🟠 Advanced |
| 12 | [Bridges, Articulation Points & SCC](#12-bridges-articulation-points--scc) | 🔴 Hard |
| 13 | [Multi-Source BFS & 0-1 BFS](#13-multi-source-bfs--0-1-bfs) | 🔴 Hard |
| 14 | [Graph DP — Longest Path, DAG DP](#14-graph-dp--longest-path-dag-dp) | 🔴 Hard |

---

## 🧠 Core Intuition

A **Graph** is a set of **nodes (vertices)** connected by **edges**. Almost every real-world problem — maps, networks, dependencies, social graphs — is a graph problem in disguise.

```
Types of Graphs:
─────────────────────────────────────────────────────
Undirected     Edges have no direction       A — B
Directed       Edges have direction          A → B
Weighted       Edges have costs/distances    A —5→ B
Unweighted     All edges have equal weight   A → B
DAG            Directed Acyclic Graph        No cycles
Tree           Connected undirected graph    n-1 edges
Bipartite      2-colorable graph             No odd cycles
```

**When to use Graph algorithms:**
- Problems involve **connections, paths, or dependencies**
- Grid problems (each cell = node, adjacent cells = edges)
- Keywords: *"connected"*, *"reachable"*, *"shortest path"*, *"cycle"*, *"prerequisite"*, *"network"*, *"island"*

---

## 📖 Graph Cheat Sheet

```
Algorithm          Best For                        Time          Space
─────────────────────────────────────────────────────────────────────
BFS                Shortest path (unweighted)      O(V+E)        O(V)
DFS                Traversal, cycle, connected     O(V+E)        O(V)
Topological Sort   Ordering with dependencies      O(V+E)        O(V)
Union-Find         Connected components, MST       O(α(n)) ≈O(1) O(V)
Dijkstra           Shortest path (non-neg weights) O(E log V)    O(V)
Bellman-Ford       Shortest path (neg weights)     O(VE)         O(V)
Floyd-Warshall     All-pairs shortest path         O(V³)         O(V²)
Kruskal            Minimum spanning tree           O(E log E)    O(V)
Prim               Minimum spanning tree (dense)   O(E log V)    O(V)
Tarjan / Kosaraju  SCC, bridges, art. points       O(V+E)        O(V)
```

---

## 1. Graph Representation

### 🔑 Concept
Before solving any graph problem, choose the right representation. The two most common are **Adjacency List** (sparse graphs) and **Adjacency Matrix** (dense graphs or small n).

### 📐 Visual
```
Graph:  0 — 1 — 2
        |       |
        3 ————— 4

Adjacency List:         Adjacency Matrix:
0: [1, 3]               [0,1,0,1,0]
1: [0, 2]               [1,0,1,0,0]
2: [1, 4]               [0,1,0,0,1]
3: [0, 4]               [1,0,0,0,1]
4: [2, 3]               [0,0,1,1,0]

Space: O(V+E)           Space: O(V²)
Best for sparse graphs  Best for dense graphs or O(1) edge lookup
```

### 🧩 Template
```javascript
// Build adjacency list from edge list (undirected)
function buildGraph(n, edges) {
    const graph = Array.from({ length: n }, () => []);

    for (const [u, v] of edges) {
        graph[u].push(v);
        graph[v].push(u);   // remove this line for directed graph
    }

    return graph;
}

// Build weighted adjacency list
function buildWeightedGraph(n, edges) {
    const graph = Array.from({ length: n }, () => []);

    for (const [u, v, w] of edges) {
        graph[u].push([v, w]);
        graph[v].push([u, w]);   // remove for directed
    }

    return graph;
}

// Build graph from grid (4-directional)
function gridToGraph(grid) {
    const rows = grid.length, cols = grid[0].length;
    const dirs = [[0,1],[0,-1],[1,0],[-1,0]];

    function neighbors(r, c) {
        return dirs
            .map(([dr, dc]) => [r + dr, c + dc])
            .filter(([nr, nc]) => nr >= 0 && nr < rows && nc >= 0 && nc < cols);
    }

    return { rows, cols, neighbors };
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [997](https://leetcode.com/problems/find-the-town-judge/) | Find the Town Judge | 🟢 Easy |
| [1791](https://leetcode.com/problems/find-center-of-star-graph/) | Find Center of Star Graph | 🟢 Easy |
| [2285](https://leetcode.com/problems/maximum-total-importance-of-roads/) | Maximum Total Importance of Roads | 🟡 Medium |
| [1615](https://leetcode.com/problems/maximal-network-rank/) | Maximal Network Rank | 🟡 Medium |

---

## 2. Breadth First Search (BFS)

### 🔑 Concept
BFS explores nodes **level by level** using a queue. It guarantees the **shortest path in an unweighted graph** because it visits all nodes at distance `d` before visiting nodes at distance `d+1`.

### 📐 Visual
```
Graph BFS from node 0:
    0
   / \
  1   2
 / \   \
3   4   5

Queue:  [0]
Visit 0 → Queue: [1, 2]
Visit 1 → Queue: [2, 3, 4]
Visit 2 → Queue: [3, 4, 5]
Visit 3 → Queue: [4, 5]
...

Distances: {0:0, 1:1, 2:1, 3:2, 4:2, 5:2}
```

### 🧩 Template
```javascript
// BFS — shortest path in unweighted graph
function bfs(graph, start) {
    const visited = new Set([start]);
    const queue = [start];
    const dist = new Map([[start, 0]]);

    while (queue.length > 0) {
        const node = queue.shift();

        for (const neighbor of graph[node]) {
            if (!visited.has(neighbor)) {
                visited.add(neighbor);
                dist.set(neighbor, dist.get(node) + 1);
                queue.push(neighbor);
            }
        }
    }

    return dist;
}

// BFS on grid — shortest path from source to target
function bfsGrid(grid, startR, startC, endR, endC) {
    const rows = grid.length, cols = grid[0].length;
    const dirs = [[0,1],[0,-1],[1,0],[-1,0]];
    const visited = Array.from({ length: rows }, () => new Array(cols).fill(false));
    const queue = [[startR, startC, 0]];   // [row, col, dist]
    visited[startR][startC] = true;

    while (queue.length > 0) {
        const [r, c, d] = queue.shift();

        if (r === endR && c === endC) return d;

        for (const [dr, dc] of dirs) {
            const nr = r + dr, nc = c + dc;
            if (nr >= 0 && nr < rows && nc >= 0 && nc < cols
                && !visited[nr][nc] && grid[nr][nc] !== 1) {
                visited[nr][nc] = true;
                queue.push([nr, nc, d + 1]);
            }
        }
    }

    return -1;   // not reachable
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [102](https://leetcode.com/problems/binary-tree-level-order-traversal/) | Binary Tree Level Order Traversal | 🟡 Medium |
| [200](https://leetcode.com/problems/number-of-islands/) | Number of Islands | 🟡 Medium |
| [994](https://leetcode.com/problems/rotting-oranges/) | Rotting Oranges | 🟡 Medium |
| [1091](https://leetcode.com/problems/shortest-path-in-binary-matrix/) | Shortest Path in Binary Matrix | 🟡 Medium |
| [127](https://leetcode.com/problems/word-ladder/) | Word Ladder | 🔴 Hard |
| [542](https://leetcode.com/problems/01-matrix/) | 01 Matrix | 🟡 Medium |
| [934](https://leetcode.com/problems/shortest-bridge/) | Shortest Bridge | 🟡 Medium |
| [2059](https://leetcode.com/problems/minimum-operations-to-convert-number/) | Minimum Operations to Convert Number | 🟡 Medium |

---

## 3. Depth First Search (DFS)

### 🔑 Concept
DFS explores as **deep as possible** before backtracking, using a stack (or recursion). Essential for **cycle detection, connected components, topological sort, path finding**, and tree/grid traversal.

### 📐 Visual
```
DFS from node 0 (recursive):
    0
   / \
  1   2
 / \
3   4

Call stack:
  dfs(0) → dfs(1) → dfs(3) → return
                  → dfs(4) → return
         → dfs(2) → return

Visit order: 0, 1, 3, 4, 2
```

### 🧩 Template
```javascript
// DFS — recursive (graph)
function dfs(graph, node, visited) {
    visited.add(node);

    for (const neighbor of graph[node]) {
        if (!visited.has(neighbor)) {
            dfs(graph, neighbor, visited);
        }
    }
}

// DFS — iterative (avoid stack overflow on large graphs)
function dfsIterative(graph, start) {
    const visited = new Set();
    const stack = [start];

    while (stack.length > 0) {
        const node = stack.pop();
        if (visited.has(node)) continue;
        visited.add(node);

        for (const neighbor of graph[node]) {
            if (!visited.has(neighbor)) {
                stack.push(neighbor);
            }
        }
    }

    return visited;
}

// DFS on grid — flood fill / island counting
function dfsGrid(grid, r, c) {
    const rows = grid.length, cols = grid[0].length;
    if (r < 0 || r >= rows || c < 0 || c >= cols) return;
    if (grid[r][c] !== '1') return;   // not land or already visited

    grid[r][c] = '0';   // mark visited (in-place)

    dfsGrid(grid, r + 1, c);
    dfsGrid(grid, r - 1, c);
    dfsGrid(grid, r, c + 1);
    dfsGrid(grid, r, c - 1);
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [200](https://leetcode.com/problems/number-of-islands/) | Number of Islands | 🟡 Medium |
| [695](https://leetcode.com/problems/max-area-of-island/) | Max Area of Island | 🟡 Medium |
| [733](https://leetcode.com/problems/flood-fill/) | Flood Fill | 🟢 Easy |
| [547](https://leetcode.com/problems/number-of-provinces/) | Number of Provinces | 🟡 Medium |
| [1254](https://leetcode.com/problems/number-of-closed-islands/) | Number of Closed Islands | 🟡 Medium |
| [417](https://leetcode.com/problems/pacific-atlantic-water-flow/) | Pacific Atlantic Water Flow | 🟡 Medium |
| [130](https://leetcode.com/problems/surrounded-regions/) | Surrounded Regions | 🟡 Medium |
| [2492](https://leetcode.com/problems/minimum-score-of-a-path-between-two-cities/) | Minimum Score of a Path Between Two Cities | 🟡 Medium |

---

## 4. Cycle Detection

### 🔑 Concept
Detecting cycles is different for **directed** and **undirected** graphs.

```
Undirected: DFS with parent tracking
  → A node is part of a cycle if a neighbor is visited AND is not its parent

Directed: DFS with 3-color marking (white/gray/black)
  → A cycle exists if we reach a GRAY node (currently in recursion stack)
```

### 📐 Visual
```
Directed Cycle Detection (3-color DFS):

  0 → 1 → 2
      ↑   ↓
      └── 3

  WHITE = unvisited, GRAY = in stack, BLACK = done

  dfs(0): mark GRAY
    dfs(1): mark GRAY
      dfs(2): mark GRAY
        dfs(3): mark GRAY
          neighbor 1 is GRAY → CYCLE DETECTED ✅
```

### 🧩 Template
```javascript
// Undirected cycle detection — DFS with parent
function hasCycleUndirected(graph, n) {
    const visited = new Set();

    function dfs(node, parent) {
        visited.add(node);
        for (const neighbor of graph[node]) {
            if (!visited.has(neighbor)) {
                if (dfs(neighbor, node)) return true;
            } else if (neighbor !== parent) {
                return true;   // visited + not parent = cycle
            }
        }
        return false;
    }

    for (let i = 0; i < n; i++) {
        if (!visited.has(i) && dfs(i, -1)) return true;
    }
    return false;
}

// Directed cycle detection — 3-color DFS
function hasCycleDirected(graph, n) {
    const WHITE = 0, GRAY = 1, BLACK = 2;
    const color = new Array(n).fill(WHITE);

    function dfs(node) {
        color[node] = GRAY;   // entering
        for (const neighbor of graph[node]) {
            if (color[neighbor] === GRAY) return true;   // back edge = cycle
            if (color[neighbor] === WHITE && dfs(neighbor)) return true;
        }
        color[node] = BLACK;  // done
        return false;
    }

    for (let i = 0; i < n; i++) {
        if (color[i] === WHITE && dfs(i)) return true;
    }
    return false;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [207](https://leetcode.com/problems/course-schedule/) | Course Schedule | 🟡 Medium |
| [802](https://leetcode.com/problems/find-eventual-safe-states/) | Find Eventual Safe States | 🟡 Medium |
| [684](https://leetcode.com/problems/redundant-connection/) | Redundant Connection | 🟡 Medium |
| [685](https://leetcode.com/problems/redundant-connection-ii/) | Redundant Connection II | 🔴 Hard |
| [1059](https://leetcode.com/problems/all-paths-from-source-lead-to-destination/) | All Paths from Source Lead to Destination | 🟡 Medium |
| [2360](https://leetcode.com/problems/longest-cycle-in-a-graph/) | Longest Cycle in a Graph | 🔴 Hard |

---

## 5. Topological Sort

### 🔑 Concept
Orders nodes in a **DAG** such that for every directed edge `u → v`, node `u` appears before `v`. Two approaches:
- **Kahn's Algorithm (BFS)** — process nodes with in-degree 0 first
- **DFS-based** — add node to result after all its descendants are processed

### 📐 Visual
```
Courses: 0→1→3
          ↓
          2→3

In-degrees: {0:0, 1:1, 2:1, 3:2}

Kahn's BFS:
  Queue: [0]   (in-degree 0)
  Process 0 → reduce neighbors 1,2 → Queue: [1,2]
  Process 1 → reduce neighbor 3   → Queue: [2]  (3 not yet 0)
  Process 2 → reduce neighbor 3   → Queue: [3]
  Process 3 → done

  Order: [0, 1, 2, 3] ✅
```

### 🧩 Template
```javascript
// Kahn's Algorithm (BFS-based topological sort)
function topoSortBFS(n, edges) {
    const graph = Array.from({ length: n }, () => []);
    const inDegree = new Array(n).fill(0);

    for (const [u, v] of edges) {
        graph[u].push(v);
        inDegree[v]++;
    }

    const queue = [];
    for (let i = 0; i < n; i++) {
        if (inDegree[i] === 0) queue.push(i);
    }

    const order = [];

    while (queue.length > 0) {
        const node = queue.shift();
        order.push(node);

        for (const neighbor of graph[node]) {
            inDegree[neighbor]--;
            if (inDegree[neighbor] === 0) queue.push(neighbor);
        }
    }

    return order.length === n ? order : [];   // [] means cycle exists
}

// DFS-based topological sort
function topoSortDFS(n, edges) {
    const graph = Array.from({ length: n }, () => []);
    for (const [u, v] of edges) graph[u].push(v);

    const visited = new Set();
    const result = [];

    function dfs(node) {
        visited.add(node);
        for (const neighbor of graph[node]) {
            if (!visited.has(neighbor)) dfs(neighbor);
        }
        result.push(node);   // push AFTER visiting all descendants
    }

    for (let i = 0; i < n; i++) {
        if (!visited.has(i)) dfs(i);
    }

    return result.reverse();   // reverse gives topological order
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [207](https://leetcode.com/problems/course-schedule/) | Course Schedule | 🟡 Medium |
| [210](https://leetcode.com/problems/course-schedule-ii/) | Course Schedule II | 🟡 Medium |
| [269](https://leetcode.com/problems/alien-dictionary/) | Alien Dictionary | 🔴 Hard |
| [444](https://leetcode.com/problems/sequence-reconstruction/) | Sequence Reconstruction | 🟡 Medium |
| [1203](https://leetcode.com/problems/sort-items-by-groups-respecting-dependencies/) | Sort Items by Groups Respecting Dependencies | 🔴 Hard |
| [2115](https://leetcode.com/problems/find-all-possible-recipes-from-given-supplies/) | Find All Possible Recipes From Given Supplies | 🟡 Medium |
| [1857](https://leetcode.com/problems/largest-color-value-in-a-directed-graph/) | Largest Color Value in a Directed Graph | 🔴 Hard |

---

## 6. Connected Components & Union-Find (DSU)

### 🔑 Concept
**Union-Find (Disjoint Set Union)** efficiently answers: *"Are these two nodes in the same component?"* Two optimizations make it near O(1) per operation:
- **Path Compression** — flatten the tree during `find`
- **Union by Rank** — always attach smaller tree under larger

### 📐 Visual
```
Union(0,1), Union(1,2), Union(3,4):

Before:  0  1  2  3  4   (5 components)
After:   0—1—2  3—4      (3 components)

parent = [0,0,0,3,3]   (0 is root of {0,1,2}, 3 is root of {3,4})

find(2): 2→0 (with path compression: parent[2]=0 directly)
union(2,3): find(2)=0, find(3)=3 → parent[3]=0
```

### 🧩 Template
```javascript
class UnionFind {
    constructor(n) {
        this.parent = Array.from({ length: n }, (_, i) => i);
        this.rank   = new Array(n).fill(0);
        this.count  = n;   // number of components
    }

    find(x) {
        if (this.parent[x] !== x) {
            this.parent[x] = this.find(this.parent[x]);   // path compression
        }
        return this.parent[x];
    }

    union(x, y) {
        const px = this.find(x), py = this.find(y);
        if (px === py) return false;   // already in same component

        // Union by rank
        if (this.rank[px] < this.rank[py]) this.parent[px] = py;
        else if (this.rank[px] > this.rank[py]) this.parent[py] = px;
        else { this.parent[py] = px; this.rank[px]++; }

        this.count--;
        return true;
    }

    connected(x, y) {
        return this.find(x) === this.find(y);
    }
}

// Usage: count connected components
function countComponents(n, edges) {
    const uf = new UnionFind(n);
    for (const [u, v] of edges) uf.union(u, v);
    return uf.count;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [547](https://leetcode.com/problems/number-of-provinces/) | Number of Provinces | 🟡 Medium |
| [684](https://leetcode.com/problems/redundant-connection/) | Redundant Connection | 🟡 Medium |
| [721](https://leetcode.com/problems/accounts-merge/) | Accounts Merge | 🟡 Medium |
| [1319](https://leetcode.com/problems/number-of-operations-to-make-network-connected/) | Number of Operations to Make Network Connected | 🟡 Medium |
| [839](https://leetcode.com/problems/similar-string-groups/) | Similar String Groups | 🔴 Hard |
| [1202](https://leetcode.com/problems/smallest-string-with-swaps/) | Smallest String With Swaps | 🟡 Medium |
| [2316](https://leetcode.com/problems/count-unreachable-pairs-of-nodes-in-an-undirected-graph/) | Count Unreachable Pairs of Nodes | 🟡 Medium |
| [2421](https://leetcode.com/problems/number-of-good-paths/) | Number of Good Paths | 🔴 Hard |

---

## 7. Bipartite Graph Check

### 🔑 Concept
A graph is **bipartite** if its nodes can be split into two groups such that every edge connects a node in group A to a node in group B — meaning it can be **2-colored** with no two adjacent nodes sharing a color. A graph is bipartite **if and only if it has no odd-length cycles**.

### 📐 Visual
```
Bipartite ✅:                Not Bipartite ✗:
  A — B — A                   A — B
  |       |                   |   |
  B       B                   B — A — B (triangle = odd cycle)

Color with BFS:
  Color 0 = RED, neighbors = BLUE, their neighbors = RED...
  If a neighbor has the SAME color as current node → NOT bipartite
```

### 🧩 Template
```javascript
// Bipartite check using BFS coloring
function isBipartite(graph) {
    const n = graph.length;
    const color = new Array(n).fill(-1);

    for (let start = 0; start < n; start++) {
        if (color[start] !== -1) continue;   // already colored

        const queue = [start];
        color[start] = 0;

        while (queue.length > 0) {
            const node = queue.shift();

            for (const neighbor of graph[node]) {
                if (color[neighbor] === -1) {
                    color[neighbor] = 1 - color[node];   // opposite color
                    queue.push(neighbor);
                } else if (color[neighbor] === color[node]) {
                    return false;   // same color = not bipartite
                }
            }
        }
    }

    return true;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [785](https://leetcode.com/problems/is-graph-bipartite/) | Is Graph Bipartite? | 🟡 Medium |
| [886](https://leetcode.com/problems/possible-bipartition/) | Possible Bipartition | 🟡 Medium |
| [2076](https://leetcode.com/problems/process-restricted-friend-requests/) | Process Restricted Friend Requests | 🔴 Hard |
| [1722](https://leetcode.com/problems/minimize-hamming-distance-after-swap-operations/) | Minimize Hamming Distance After Swap Operations | 🟡 Medium |

---

## 8. Shortest Path — Dijkstra's Algorithm

### 🔑 Concept
Finds the **shortest path from a source** to all nodes in a graph with **non-negative edge weights**. Uses a **min-heap (priority queue)** to always process the closest unvisited node next.

```
⚠️ Does NOT work with negative edge weights → use Bellman-Ford
✅ Greedy: once a node is popped from heap, its distance is final
```

### 📐 Visual
```
Graph:  0 —1→ 1 —4→ 3
         \         ↗
          —2→ 2 —1→

Dijkstra from 0:
  Heap: [(0,0)]
  Pop (0,0): dist[0]=0 → push (1,1),(2,2)
  Pop (1,1): dist[1]=1 → push (5,3)
  Pop (2,2): dist[2]=2 → push (3,3)
  Pop (3,3): dist[3]=3 ← shorter than (5,3) ✅

  Distances: {0:0, 1:1, 2:2, 3:3}
```

### 🧩 Template
```javascript
// Dijkstra's algorithm (min-heap simulation with sorted array)
function dijkstra(graph, start, n) {
    const dist = new Array(n).fill(Infinity);
    dist[start] = 0;

    // Min-heap: [distance, node]
    // Using sorted array for clarity — use a proper heap in production
    const heap = [[0, start]];

    while (heap.length > 0) {
        heap.sort((a, b) => a[0] - b[0]);
        const [d, node] = heap.shift();

        if (d > dist[node]) continue;   // stale entry

        for (const [neighbor, weight] of graph[node]) {
            const newDist = dist[node] + weight;
            if (newDist < dist[neighbor]) {
                dist[neighbor] = newDist;
                heap.push([newDist, neighbor]);
            }
        }
    }

    return dist;
}

// Network Delay Time — classic Dijkstra application
function networkDelayTime(times, n, k) {
    const graph = Array.from({ length: n + 1 }, () => []);
    for (const [u, v, w] of times) graph[u].push([v, w]);

    const dist = dijkstra(graph, k, n + 1);

    const maxDist = Math.max(...dist.slice(1));
    return maxDist === Infinity ? -1 : maxDist;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [743](https://leetcode.com/problems/network-delay-time/) | Network Delay Time | 🟡 Medium |
| [1514](https://leetcode.com/problems/path-with-maximum-probability/) | Path With Maximum Probability | 🟡 Medium |
| [1631](https://leetcode.com/problems/path-with-minimum-effort/) | Path With Minimum Effort | 🟡 Medium |
| [787](https://leetcode.com/problems/cheapest-flights-within-k-stops/) | Cheapest Flights Within K Stops | 🟡 Medium |
| [1976](https://leetcode.com/problems/number-of-ways-to-arrive-at-destination/) | Number of Ways to Arrive at Destination | 🟡 Medium |
| [2642](https://leetcode.com/problems/design-graph-with-shortest-path-calculator/) | Design Graph With Shortest Path Calculator | 🔴 Hard |
| [2045](https://leetcode.com/problems/second-minimum-time-to-reach-destination/) | Second Minimum Time to Reach Destination | 🔴 Hard |

---

## 9. Shortest Path — Bellman-Ford & SPFA

### 🔑 Concept
**Bellman-Ford** handles **negative edge weights** by relaxing all edges `V-1` times. If a distance still decreases on the `V`th relaxation, a **negative cycle** exists. **SPFA** (Shortest Path Faster Algorithm) is an optimized BFS-based variant.

```
Dijkstra  → O(E log V), no negative weights
Bellman-Ford → O(VE), handles negative weights, detects negative cycles
```

### 🧩 Template
```javascript
// Bellman-Ford algorithm
function bellmanFord(n, edges, source) {
    const dist = new Array(n).fill(Infinity);
    dist[source] = 0;

    // Relax all edges V-1 times
    for (let i = 0; i < n - 1; i++) {
        let updated = false;
        for (const [u, v, w] of edges) {
            if (dist[u] !== Infinity && dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                updated = true;
            }
        }
        if (!updated) break;   // early exit if no update
    }

    // Check for negative cycles
    for (const [u, v, w] of edges) {
        if (dist[u] !== Infinity && dist[u] + w < dist[v]) {
            return null;   // negative cycle detected
        }
    }

    return dist;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [787](https://leetcode.com/problems/cheapest-flights-within-k-stops/) | Cheapest Flights Within K Stops | 🟡 Medium |
| [743](https://leetcode.com/problems/network-delay-time/) | Network Delay Time | 🟡 Medium |
| [1334](https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/) | Find the City With the Smallest Number of Neighbors | 🟡 Medium |
| [2589](https://leetcode.com/problems/minimum-time-to-complete-all-tasks/) | Minimum Time to Complete All Tasks | 🔴 Hard |

---

## 10. Shortest Path — Floyd-Warshall (All Pairs)

### 🔑 Concept
Finds **shortest paths between ALL pairs of nodes** in O(V³). Works with negative weights (but not negative cycles). The key idea: for each intermediate node `k`, check if going through `k` improves the path from `i` to `j`.

```
dp[i][j][k] = shortest path from i to j using only nodes 0..k as intermediates
Transition:  dp[i][j] = min(dp[i][j], dp[i][k] + dp[k][j])
```

### 🧩 Template
```javascript
// Floyd-Warshall all-pairs shortest path
function floydWarshall(n, edges) {
    const INF = Infinity;
    const dist = Array.from({ length: n }, (_, i) =>
        Array.from({ length: n }, (_, j) => (i === j ? 0 : INF))
    );

    // Initialize with direct edges
    for (const [u, v, w] of edges) {
        dist[u][v] = Math.min(dist[u][v], w);
        dist[v][u] = Math.min(dist[v][u], w);   // remove for directed
    }

    // Relax through each intermediate node k
    for (let k = 0; k < n; k++) {
        for (let i = 0; i < n; i++) {
            for (let j = 0; j < n; j++) {
                if (dist[i][k] + dist[k][j] < dist[i][j]) {
                    dist[i][j] = dist[i][k] + dist[k][j];
                }
            }
        }
    }

    return dist;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [1334](https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/) | Find the City With Smallest Number of Neighbors | 🟡 Medium |
| [2642](https://leetcode.com/problems/design-graph-with-shortest-path-calculator/) | Design Graph With Shortest Path Calculator | 🔴 Hard |
| [399](https://leetcode.com/problems/evaluate-division/) | Evaluate Division | 🟡 Medium |
| [1462](https://leetcode.com/problems/course-schedule-iv/) | Course Schedule IV | 🟡 Medium |

---

## 11. Minimum Spanning Tree — Kruskal & Prim

### 🔑 Concept
A **Minimum Spanning Tree (MST)** connects all nodes with the minimum total edge weight using exactly `V-1` edges.

```
Kruskal  — Sort edges by weight, add edge if it doesn't form a cycle (use DSU)
           Best for sparse graphs  O(E log E)

Prim     — Start from any node, greedily add the cheapest edge connecting
           the MST to a new node  O(E log V) with heap
           Best for dense graphs
```

### 📐 Visual
```
Graph edges (sorted by weight):
  (1,2,1), (0,3,2), (0,1,4), (2,3,3), (1,3,5)

Kruskal:
  Add (1,2,1) → no cycle ✅  MST weight=1
  Add (0,3,2) → no cycle ✅  MST weight=3
  Add (2,3,3) → no cycle ✅  MST weight=6
  Skip (0,1,4) → would form cycle
  Done! V-1=3 edges added ✅
```

### 🧩 Template
```javascript
// Kruskal's MST using Union-Find
function kruskalMST(n, edges) {
    edges.sort((a, b) => a[2] - b[2]);   // sort by weight

    const uf = new UnionFind(n);
    let totalWeight = 0;
    let edgesUsed = 0;
    const mst = [];

    for (const [u, v, w] of edges) {
        if (uf.union(u, v)) {     // no cycle
            totalWeight += w;
            mst.push([u, v, w]);
            edgesUsed++;
            if (edgesUsed === n - 1) break;   // MST complete
        }
    }

    return edgesUsed === n - 1 ? { weight: totalWeight, edges: mst } : null;
}

// Prim's MST
function primMST(graph, n) {
    const inMST = new Array(n).fill(false);
    const minCost = new Array(n).fill(Infinity);
    minCost[0] = 0;
    let totalWeight = 0;

    for (let i = 0; i < n; i++) {
        // Pick vertex with minimum cost not yet in MST
        let u = -1;
        for (let v = 0; v < n; v++) {
            if (!inMST[v] && (u === -1 || minCost[v] < minCost[u])) u = v;
        }

        inMST[u] = true;
        totalWeight += minCost[u];

        for (const [neighbor, weight] of graph[u]) {
            if (!inMST[neighbor] && weight < minCost[neighbor]) {
                minCost[neighbor] = weight;
            }
        }
    }

    return totalWeight;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [1584](https://leetcode.com/problems/min-cost-to-connect-all-points/) | Min Cost to Connect All Points | 🟡 Medium |
| [1135](https://leetcode.com/problems/connecting-cities-with-minimum-cost/) | Connecting Cities With Minimum Cost | 🟡 Medium |
| [1168](https://leetcode.com/problems/optimize-water-distribution-in-a-village/) | Optimize Water Distribution in a Village | 🔴 Hard |
| [1489](https://leetcode.com/problems/find-critical-and-pseudo-critical-edges-in-minimum-spanning-tree/) | Find Critical and Pseudo-Critical Edges in MST | 🔴 Hard |
| [1724](https://leetcode.com/problems/checking-existence-of-edge-length-limited-paths/) | Checking Existence of Edge Length Limited Paths | 🔴 Hard |

---

## 12. Bridges, Articulation Points & SCC

### 🔑 Concept
Advanced DFS applications:
- **Bridge** — edge whose removal disconnects the graph
- **Articulation Point** — node whose removal disconnects the graph
- **SCC (Strongly Connected Component)** — maximal set of nodes where every node is reachable from every other

```
Algorithm       Finds                    Time
──────────────────────────────────────────────
Tarjan's DFS    Bridges + Art. Points    O(V+E)
Kosaraju's      SCCs                     O(V+E)
Tarjan's SCC    SCCs                     O(V+E)
```

### 🧩 Template
```javascript
// Find all bridges using Tarjan's algorithm
function findBridges(n, edges) {
    const graph = Array.from({ length: n }, () => []);
    for (const [u, v] of edges) {
        graph[u].push(v);
        graph[v].push(u);
    }

    const disc   = new Array(n).fill(-1);   // discovery time
    const low    = new Array(n).fill(-1);   // lowest disc reachable
    const bridges = [];
    let timer = 0;

    function dfs(node, parent) {
        disc[node] = low[node] = timer++;

        for (const neighbor of graph[node]) {
            if (neighbor === parent) continue;

            if (disc[neighbor] === -1) {
                dfs(neighbor, node);
                low[node] = Math.min(low[node], low[neighbor]);

                if (low[neighbor] > disc[node]) {
                    bridges.push([node, neighbor]);   // bridge found!
                }
            } else {
                low[node] = Math.min(low[node], disc[neighbor]);
            }
        }
    }

    for (let i = 0; i < n; i++) {
        if (disc[i] === -1) dfs(i, -1);
    }

    return bridges;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [1192](https://leetcode.com/problems/critical-connections-in-a-network/) | Critical Connections in a Network | 🔴 Hard |
| [1568](https://leetcode.com/problems/minimum-number-of-days-to-disconnect-island/) | Minimum Number of Days to Disconnect Island | 🔴 Hard |
| [2360](https://leetcode.com/problems/longest-cycle-in-a-graph/) | Longest Cycle in a Graph | 🔴 Hard |
| [802](https://leetcode.com/problems/find-eventual-safe-states/) | Find Eventual Safe States | 🟡 Medium |

---

## 13. Multi-Source BFS & 0-1 BFS

### 🔑 Concept
**Multi-Source BFS** — start BFS from **multiple sources simultaneously**. All sources go into the queue at distance 0. Useful for problems like "distance to nearest X".

**0-1 BFS** — graph with edge weights of only **0 or 1**. Use a **deque**: weight-0 edges push to front (free move), weight-1 edges push to back. Runs in O(V+E), faster than Dijkstra.

### 📐 Visual
```
Multi-Source BFS (01 Matrix — distance to nearest 0):
  Grid:  1 1 0
         1 1 1
         0 1 1

  Start: all 0s in queue → [(0,2,0), (2,0,0)]
  Expand outward simultaneously:
    (0,2) → neighbors (0,1,1),(1,2,1)
    (2,0) → neighbors (1,0,1),(2,1,1)
  Continue until all cells filled
```

### 🧩 Template
```javascript
// Multi-Source BFS
function multiSourceBFS(grid, sources) {
    const rows = grid.length, cols = grid[0].length;
    const dist = Array.from({ length: rows }, () => new Array(cols).fill(Infinity));
    const queue = [];
    const dirs = [[0,1],[0,-1],[1,0],[-1,0]];

    for (const [r, c] of sources) {
        dist[r][c] = 0;
        queue.push([r, c]);
    }

    while (queue.length > 0) {
        const [r, c] = queue.shift();
        for (const [dr, dc] of dirs) {
            const nr = r + dr, nc = c + dc;
            if (nr >= 0 && nr < rows && nc >= 0 && nc < cols
                && dist[nr][nc] === Infinity) {
                dist[nr][nc] = dist[r][c] + 1;
                queue.push([nr, nc]);
            }
        }
    }

    return dist;
}

// 0-1 BFS using deque
function zeroOneBFS(graph, start, n) {
    const dist = new Array(n).fill(Infinity);
    dist[start] = 0;
    const deque = [start];

    while (deque.length > 0) {
        const node = deque.shift();

        for (const [neighbor, weight] of graph[node]) {
            const newDist = dist[node] + weight;
            if (newDist < dist[neighbor]) {
                dist[neighbor] = newDist;
                if (weight === 0) deque.unshift(neighbor);  // front
                else deque.push(neighbor);                   // back
            }
        }
    }

    return dist;
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [542](https://leetcode.com/problems/01-matrix/) | 01 Matrix | 🟡 Medium |
| [994](https://leetcode.com/problems/rotting-oranges/) | Rotting Oranges | 🟡 Medium |
| [1162](https://leetcode.com/problems/as-far-from-land-as-possible/) | As Far from Land as Possible | 🟡 Medium |
| [2290](https://leetcode.com/problems/minimum-obstacle-removal-to-reach-corner/) | Minimum Obstacle Removal to Reach Corner | 🔴 Hard |
| [2493](https://leetcode.com/problems/divide-nodes-into-the-maximum-number-of-groups/) | Divide Nodes Into the Maximum Number of Groups | 🔴 Hard |
| [2608](https://leetcode.com/problems/shortest-cycle-in-a-graph/) | Shortest Cycle in a Graph | 🔴 Hard |

---

## 14. Graph DP — Longest Path, DAG DP

### 🔑 Concept
Dynamic programming on graphs — especially on **DAGs** (no cycles means we can compute DP values in topological order). Common patterns: longest path in a DAG, number of paths, minimum cost path with constraints.

### 📐 Visual
```
Longest Path in DAG:
  0 → 1 → 3
  ↓       ↑
  2 ———————

  Topo order: [0, 1, 2, 3]

  dp[0] = 0
  dp[1] = dp[0] + 1 = 1
  dp[2] = dp[0] + 1 = 1
  dp[3] = max(dp[1]+1, dp[2]+1) = 2

  Longest path = 2 ✅
```

### 🧩 Template
```javascript
// Longest path in a DAG
function longestPathDAG(n, edges) {
    const graph = Array.from({ length: n }, () => []);
    const inDegree = new Array(n).fill(0);

    for (const [u, v] of edges) {
        graph[u].push(v);
        inDegree[v]++;
    }

    // Kahn's topo sort + DP
    const dp = new Array(n).fill(0);
    const queue = [];

    for (let i = 0; i < n; i++) {
        if (inDegree[i] === 0) queue.push(i);
    }

    while (queue.length > 0) {
        const node = queue.shift();

        for (const neighbor of graph[node]) {
            dp[neighbor] = Math.max(dp[neighbor], dp[node] + 1);
            inDegree[neighbor]--;
            if (inDegree[neighbor] === 0) queue.push(neighbor);
        }
    }

    return Math.max(...dp);
}

// Count paths from source to target in DAG
function countPaths(n, graph, source, target) {
    const memo = new Array(n).fill(-1);

    function dfs(node) {
        if (node === target) return 1;
        if (memo[node] !== -1) return memo[node];

        let count = 0;
        for (const neighbor of graph[node]) {
            count += dfs(neighbor);
        }
        return memo[node] = count;
    }

    return dfs(source);
}
```

### 📚 LeetCode Problems

| # | Problem | Difficulty |
|---|---------|------------|
| [1857](https://leetcode.com/problems/largest-color-value-in-a-directed-graph/) | Largest Color Value in a Directed Graph | 🔴 Hard |
| [1976](https://leetcode.com/problems/number-of-ways-to-arrive-at-destination/) | Number of Ways to Arrive at Destination | 🟡 Medium |
| [329](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/) | Longest Increasing Path in a Matrix | 🔴 Hard |
| [2050](https://leetcode.com/problems/parallel-courses-iii/) | Parallel Courses III | 🔴 Hard |
| [1059](https://leetcode.com/problems/all-paths-from-source-lead-to-destination/) | All Paths from Source Lead to Destination | 🟡 Medium |
| [2192](https://leetcode.com/problems/all-ancestors-of-a-node-in-a-directed-acyclic-graph/) | All Ancestors of a Node in a DAG | 🟡 Medium |

---

## 🗺️ Pattern Decision Tree

```
What type of traversal do you need?
├── Level-by-level / Shortest path (unweighted) → Pattern 2: BFS
└── Deep exploration / Path existence           → Pattern 3: DFS

Does the graph have DIRECTED EDGES and possible ORDERING?
└── YES → Pattern 5: Topological Sort

Do you need to check CONNECTIVITY or MERGE components?
├── Static graph   → Pattern 6: Union-Find (DSU)
└── Dynamic unions → Pattern 6: Union-Find (DSU)

Does the graph need to be 2-COLORED?
└── YES → Pattern 7: Bipartite Check

Is there a CYCLE in the graph?
├── Undirected → Pattern 4: DFS + parent check
└── Directed   → Pattern 4: 3-color DFS

Do you need the SHORTEST PATH?
├── Unweighted               → Pattern 2: BFS
├── Non-negative weights     → Pattern 8: Dijkstra
├── Negative weights         → Pattern 9: Bellman-Ford
└── All pairs                → Pattern 10: Floyd-Warshall

Do you need to CONNECT all nodes with minimum cost?
└── YES → Pattern 11: MST (Kruskal or Prim)

Multiple starting sources or 0/1 edge weights?
├── Multiple sources simultaneously → Pattern 13: Multi-Source BFS
└── Edges are 0 or 1               → Pattern 13: 0-1 BFS

Is the graph a DAG with optimization over paths?
└── YES → Pattern 14: Graph DP

Do you need BRIDGES, ARTICULATION POINTS, or SCCs?
└── YES → Pattern 12: Tarjan / Kosaraju
```

---

## ⚡ Quick Complexity Reference

| Pattern | Time | Space |
|---------|------|-------|
| Build Adjacency List | O(V+E) | O(V+E) |
| BFS | O(V+E) | O(V) |
| DFS | O(V+E) | O(V) |
| Cycle Detection | O(V+E) | O(V) |
| Topological Sort | O(V+E) | O(V) |
| Union-Find | O(α(n)) ≈ O(1) | O(V) |
| Bipartite Check | O(V+E) | O(V) |
| Dijkstra | O(E log V) | O(V) |
| Bellman-Ford | O(VE) | O(V) |
| Floyd-Warshall | O(V³) | O(V²) |
| Kruskal MST | O(E log E) | O(V) |
| Prim MST | O(E log V) | O(V) |
| Bridges / Art. Points | O(V+E) | O(V) |
| Multi-Source BFS | O(V+E) | O(V) |
| 0-1 BFS | O(V+E) | O(V) |
| DAG DP | O(V+E) | O(V) |

---

## 🏆 Curated Must-Solve List (Top 30)

| # | Problem | Pattern | Company Tags |
|---|---------|---------|--------------|
| [127](https://leetcode.com/problems/word-ladder/) | Word Ladder | BFS | Amazon, Meta |
| [130](https://leetcode.com/problems/surrounded-regions/) | Surrounded Regions | DFS | Google |
| [200](https://leetcode.com/problems/number-of-islands/) | Number of Islands | BFS/DFS | Amazon, Meta, Google |
| [207](https://leetcode.com/problems/course-schedule/) | Course Schedule | Topo Sort / Cycle | Meta, Amazon |
| [210](https://leetcode.com/problems/course-schedule-ii/) | Course Schedule II | Topo Sort | Google, Meta |
| [269](https://leetcode.com/problems/alien-dictionary/) | Alien Dictionary | Topo Sort | Meta, Amazon |
| [329](https://leetcode.com/problems/longest-increasing-path-in-a-matrix/) | Longest Increasing Path in Matrix | Graph DP | Google |
| [399](https://leetcode.com/problems/evaluate-division/) | Evaluate Division | BFS/Floyd | Google, Meta |
| [417](https://leetcode.com/problems/pacific-atlantic-water-flow/) | Pacific Atlantic Water Flow | DFS | Google |
| [542](https://leetcode.com/problems/01-matrix/) | 01 Matrix | Multi-Source BFS | Amazon |
| [547](https://leetcode.com/problems/number-of-provinces/) | Number of Provinces | DSU/DFS | Google |
| [684](https://leetcode.com/problems/redundant-connection/) | Redundant Connection | DSU | Amazon |
| [695](https://leetcode.com/problems/max-area-of-island/) | Max Area of Island | DFS | Amazon, Google |
| [721](https://leetcode.com/problems/accounts-merge/) | Accounts Merge | DSU | Google, Meta |
| [743](https://leetcode.com/problems/network-delay-time/) | Network Delay Time | Dijkstra | Amazon |
| [785](https://leetcode.com/problems/is-graph-bipartite/) | Is Graph Bipartite? | Bipartite | Google |
| [787](https://leetcode.com/problems/cheapest-flights-within-k-stops/) | Cheapest Flights Within K Stops | Bellman-Ford | Amazon |
| [802](https://leetcode.com/problems/find-eventual-safe-states/) | Find Eventual Safe States | Cycle/Topo | Google |
| [886](https://leetcode.com/problems/possible-bipartition/) | Possible Bipartition | Bipartite/DSU | Amazon |
| [994](https://leetcode.com/problems/rotting-oranges/) | Rotting Oranges | Multi-Source BFS | Amazon |
| [1091](https://leetcode.com/problems/shortest-path-in-binary-matrix/) | Shortest Path in Binary Matrix | BFS | Amazon |
| [1192](https://leetcode.com/problems/critical-connections-in-a-network/) | Critical Connections | Bridges | Amazon |
| [1319](https://leetcode.com/problems/number-of-operations-to-make-network-connected/) | Number of Operations to Make Network Connected | DSU | Amazon |
| [1334](https://leetcode.com/problems/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance/) | Find the City With Smallest Number of Neighbors | Floyd | Amazon |
| [1462](https://leetcode.com/problems/course-schedule-iv/) | Course Schedule IV | Floyd/Topo | Google |
| [1514](https://leetcode.com/problems/path-with-maximum-probability/) | Path With Maximum Probability | Dijkstra | Google |
| [1584](https://leetcode.com/problems/min-cost-to-connect-all-points/) | Min Cost to Connect All Points | MST | Google |
| [1631](https://leetcode.com/problems/path-with-minimum-effort/) | Path With Minimum Effort | Dijkstra | Google |
| [1857](https://leetcode.com/problems/largest-color-value-in-a-directed-graph/) | Largest Color Value in a Directed Graph | Graph DP | Amazon |
| [2045](https://leetcode.com/problems/second-minimum-time-to-reach-destination/) | Second Minimum Time to Reach Destination | Dijkstra | Amazon |

---

## 📅 Study Plan

| Week | Focus | Patterns |
|------|-------|----------|
| Week 1 | Foundation — representation, BFS, DFS | Patterns 1, 2, 3 |
| Week 2 | Cycle detection & topological sort | Patterns 4, 5 |
| Week 3 | Connected components & bipartite | Patterns 6, 7 |
| Week 4 | Shortest paths — Dijkstra | Pattern 8 |
| Week 5 | Shortest paths — Bellman-Ford, Floyd | Patterns 9, 10 |
| Week 6 | MST & advanced BFS | Patterns 11, 13 |
| Week 7 | Bridges, SCC & Graph DP | Patterns 12, 14 |
| Week 8 | Mock interviews | Top 30 Must-Solve List |

---

## 🔗 Related Repos

> Build your complete DSA pattern library:

-  [Sliding Window Patterns](https://github.com/lokeshcs25/Sliding-Window-Patterns-From-One-to-N)
-  [Two Pointer Patterns](https://github.com/lokeshcs25/Two-Pointer-Patterns-From-Zero-to-N)
-  [Binary Search Patterns](https://github.com/lokeshcs25/Binary-Search-Patterns-From-One-to-N)
-  [Bit Manipulation Patterns](https://github.com/lokeshcs25/Bit-Manipulation-Patterns-From-One-to-N)
-  [Greedy Algorithm Patterns](https://github.com/lokeshcs25/Greedy-Patterns-From-One-to-N)
-  **Graph Algorithm Patterns** - *you're here*
-   Tree Traversal Patterns — *Coming soon*
-   Dynamic Programming Patterns — *Coming soon*

---

## 🤝 Contributing

Contributions are welcome!
- Found a better problem for a pattern? Open a PR.
- Want to add solutions in Python / Java / C++? Go for it.
- Spot an error? File an issue.

Please follow the existing format when adding problems.

---

## ⭐ Star this repo if it helped you!

> Part of a complete DSA patterns series. One pattern at a time, all the way to FAANG. Good luck! 🚀
