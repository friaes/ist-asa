# ASA — Course Projects

Two C++ projects for the **Análise e Síntese de Algoritmos** (Analysis and Synthesis of Algorithms / ASA) course at **Instituto Superior Técnico (IST)**, University of Lisbon, 2022/23.

ASA is the advanced algorithms course: it's about **designing algorithms with the right paradigm** for a problem and **analysing their complexity**. The two projects each target a classic paradigm — the first exhaustive search / backtracking on a combinatorial counting problem, the second a greedy graph algorithm.

| # | Project | Paradigm | Core technique |
|---|---------|----------|----------------|
| 1 | [Staircase tiling](#project-1--staircase-tiling) | Backtracking / exhaustive enumeration | Recursive placement of square tiles |
| 2 | [Maximum rail network](#project-2--maximum-rail-network) | Greedy | Kruskal's algorithm + Union–Find |

## Project 1 — Staircase tiling

**Problem.** A staircase-shaped region is carved out of an `n × m` grid: for each row `i` the staircase reaches column `cᵢ` (with `cᵢ ≥ cᵢ₋₁`). The region must be **completely covered by square tiles** of any integer size (1×1, 2×2, 3×3, …), using as many tiles of each size as needed. The task is to **count the number of distinct tilings** of the region. (If the region is empty — all `cᵢ = 0` — the answer is 0.)

**Approach.** `projeto1.cpp` solves it by **recursive backtracking** over a `busy` occupancy matrix:

- It finds the first free cell and places the **largest square tile** that fits within the staircase boundary there (`maxTileSize`, `putTile`).
- It recurses to fill the rest of the region, counting a configuration each time the region is fully covered.
- On backtracking it **shrinks the last tile by one unit** (`decreaseTile`) and re-explores, so that every combination of tile sizes that covers the area is enumerated exactly once.

The counting is done with `unsigned long long` because the number of configurations grows quickly. This is the "synthesis" side of the course — picking exhaustive search with careful pruning — paired with reasoning about how the count (and the running time) scales with the region.

**Input / output.**
```
n            # number of rows
m            # number of columns
c_1          # staircase column for row 1
...          #   (one per row, non-decreasing)
c_n
```
Output: the number of distinct tilings.

**Example** (from the assignment, a 4×5 grid with staircase `0, 2, 3, 5`):
```
4
5
0
2
3
5
```
produces `4` — the four valid tilings of that region.

## Project 2 — Maximum rail network

**Problem.** Regions of a country are to be connected by high-speed rail. The regions and the possible links between neighbouring regions form an undirected weighted graph `G = (V, E)` (the assignment motivates it via a Delaunay triangulation of the station locations), where each edge weight is the **commercial-trade value** of that link. The goal is to **maximise the total trade value carried by rail while building as few links as possible** — i.e. connect the regions using a subset of edges that is a tree and has maximum total weight. Some regions may not be connectable at all, so the graph can be disconnected.

**Approach.** This is a **Maximum Spanning Tree** (really a maximum spanning *forest*, since the graph may be disconnected), solved with **Kruskal's algorithm**:

- Sort all edges by weight in **descending** order (`compareCost`).
- Iterate the edges, adding one whenever its endpoints are in different components, until the spanning forest is complete.
- Component membership is tracked with a **Union–Find (disjoint-set) structure** implementing both standard optimisations: **path compression** in `FindSet` and **union by rank** in `Union`, giving effectively near-linear total time.

The program outputs the total weight of the maximum spanning forest. `ios::sync_with_stdio(false)` is used to speed up input on large graphs.

**Input / output.**
```
|V|                 # number of vertices (>= 1)
|E|                 # number of edges (>= 0)
u v w               # |E| lines: edge between u and v with weight w
...
```
Output: the maximum total trade value.

**Example.** A graph on 4 vertices with edges `1-2:1, 2-3:2, 3-4:3, 1-4:4, 1-3:5` yields `11` (the tree `1-3`, `1-4`, `2-3`).

## Building & running

Each project is a single C++ file:

```bash
g++ -std=c++17 -O2 -o proj1 projeto1.cpp
g++ -std=c++17 -O2 -o proj2 projeto2.cpp

./proj1 < input1.txt
./proj2 < input2.txt
```

## Repository layout

```
.
├── projeto1.cpp   # Project 1 — staircase tiling (backtracking)
├── projeto2.cpp   # Project 2 — maximum spanning forest (Kruskal + Union-Find)
├── p1.pdf         # Project 1 assignment (pt)
├── p2.pdf         # Project 2 assignment (pt)
└── README.md
```

## Authors

José António Lopes (103938) and Rodrigo Friães (104139).
