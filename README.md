# Group_2_Graph-Theory_Planarity

## Group Members

| No. | Name                         | NRP        |
| --: | ---------------------------- | ---------- |
|   1 | Ahmad Farras Favian Al Efasi | 5025251005 |
|   2 | Daniel Pedrosaputra          | 5025251171 |

## Algorithm Introduction

### Demoucron-Malgrange-Pertuiset (DMP)

DMP (Demoucron, Malgrange and Pertuiset) is a constructive planarity test. Instead of searching for a forbidden subgraph, it tries to build a crossing-free drawing one piece at a time. If the drawing can be completed, the graph is planar. If it gets stuck, the graph is non-planar. Because a graph is planar if and only if each of its biconnected components is planar, the algorithm is run on each component separately, and self-loops and parallel edges are ignored since they do not affect planarity.

The algorithm keeps an already-drawn subgraph **H**, whose edges divide the plane into faces. The remaining graph is described in terms of three ideas:

- **Fragment:** either a single edge outside H whose endpoints are both in H, or a connected component of G − V(H) together with the edges joining it to H.
- **Attachment vertices:** the vertices of a fragment that lie on H.
- **Admissible face:** a face of H whose boundary contains all attachment vertices of a fragment. A fragment can only be drawn inside an admissible face.

#### Steps

1. Find any cycle in the component and draw it as the initial H. It has two faces (inside and outside).
2. Compute all fragments of H in G.
3. For each fragment, find its admissible faces.
4. If any fragment has no admissible face, stop: the graph is **non-planar**.
5. Otherwise, choose a fragment, preferring one with exactly one admissible face, since its placement is forced.
6. Pick a path through the chosen fragment between two of its attachment vertices, draw it inside an admissible face (splitting that face in two), and add it to H.
7. Repeat from step 2 until every edge is in H. Then the graph is **planar**, and the faces of H form a valid embedding.

Placing forced fragments first means the algorithm never has to undo a choice. A planar graph has at most 3v − 6 edges, so there are only O(v) path insertions, each costing O(v), which gives an overall running time of **O(v²)**.

The main drawback of DMP is that it is quadratic, so it is slower than the linear-time Left-Right test on large graphs, and it requires the graph to be split into biconnected components first.

## Implementations

## DMP

The DMP Planarity Checker is implemented in Python using an adjacency-list representation of an undirected graph. The implementation is divided into several functions, with each function handling a specific part of the planarity-testing process.

#### 1. Graph Representation

The graph is represented using a dictionary in which each vertex stores a set of its adjacent vertices. This structure makes it easier to access neighboring vertices and check whether an edge exists.

For example, an edge between vertices `0` and `1` is stored in both adjacency lists because the graph is undirected.

Before processing the graph, the program also checks the input to ensure that every edge connects declared vertices and that no self-loops are included.

#### 2. Biconnected Component Decomposition

The function `find_biconnected_components()` uses Tarjan's Depth-First Search (DFS) technique to divide the graph into biconnected components, also called blocks.

This step is important because a graph may contain articulation points, which are vertices whose removal disconnects the graph. By processing each block separately, the program can handle graphs that contain articulation points or disconnected components.

#### 3. Finding the Initial Cycle

The function `find_cycle()` searches for a simple cycle inside each non-trivial biconnected component.

This cycle becomes the starting point of the planar embedding. The program initially creates two faces from the cycle, representing its two sides.

The vertices and edges belonging to the initial cycle are marked as embedded so that the algorithm can identify the remaining parts of the graph.

#### 4. Identifying Fragments

The function `get_fragments()` identifies the remaining parts of the graph that have not yet been inserted into the embedding.

There are two types of fragments:

- **Component fragments:** Connected groups of unembedded vertices and their edges connecting them to already embedded vertices.
- **Chord fragments:** Unused edges that connect two vertices that are already part of the embedded subgraph.

The vertices where a fragment connects to the embedded subgraph are called its contact vertices.

#### 5. Checking Admissible Faces

The function `admissible_faces()` determines which existing faces can accommodate a particular fragment.

A face is considered admissible when all contact vertices of the fragment are present on that face's boundary.

The algorithm calculates the admissible faces for each remaining fragment and prioritizes the fragment with the fewest available faces.

If a fragment has no admissible face, the program reports that the current embedding cannot be extended and classifies the block as non-planar.

#### 6. Finding an Alpha-Path

The function `find_alpha_path()` searches for a path connecting two contact vertices through the selected fragment.

This path is called an **alpha-path**. Its endpoints must lie on the selected face, while its internal vertices belong to the unembedded part of the fragment.

For chord fragments, the alpha-path is simply the unused edge connecting the two contact vertices.

#### 7. Updating the Planar Embedding

After finding an alpha-path, the function `split_face()` inserts the path into the selected face.

The inserted path divides the original face into two new faces. The program then updates the face list, marks the path's edges as used, and adds its vertices to the embedded subgraph.

This process is repeated until all fragments have been processed or a fragment with no admissible face is found.

#### 8. Final Planarity Result

The main algorithm is handled by `test_block_dmp()` and `is_planar_dmp()`.

- `test_block_dmp()` performs the DMP process on one biconnected component.
- `is_planar_dmp()` processes all components and combines their results.

If every block can be embedded without encountering a fragment that has no admissible face, the graph is classified as planar. Otherwise, the graph is classified as non-planar.

The program returns the result together with information about the blocks, embedding faces, and processing steps.

#### 9. Command-Line Interface

The file `main.py` provides a simple command-line interface for interacting with the algorithm.

The user enters the number of vertices, the number of edges, and the endpoints of each edge. The program passes this input to `is_planar_dmp()` and displays whether the graph is planar or non-planar.

For planar graphs, the program also displays the resulting face boundaries. For non-planar graphs, it displays the block and the processing information that led to the result.

#### Implementation Note

This project is an educational implementation of the DMP path-insertion approach. It prioritizes clarity and understanding of the algorithm over optimization. Its implementation and results should be checked against additional test graphs before being used as a general-purpose planarity checker.
