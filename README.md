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
