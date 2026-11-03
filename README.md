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

### Schmidt's Algorithm

Schmidt’s algorithm is an efficient, incremental vertex-addition planarity test. Instead of searching for a forbidden subgraph or checking global conflict graphs, it constructs a planar embedding by adding vertices one by one in a specific order derived from a Depth-First Search (DFS) tree. If all vertices can be added and successfully embedded into active face boundaries without edge crossings, the graph is planar. If any vertex or its incident edges cannot be accommodated, the algorithm stops and correctly classifies the graph as non-planar.

Because planarity applies to a graph if and only if each of its biconnected components is planar, the algorithm first decomposes the graph into biconnected components (blocks) using Tarjan's DFS technique. Each block is then processed independently to ensure that articulation points or disconnected components do not interfere with the overall evaluation.

The algorithm maintains an already-embedded subgraph and processes incoming vertices by examining how they connect to existing parts of the structure. The approach relies on several key concepts:

- **DFS Vertex Ordering:** Vertices are ordered via a Depth-First Search tree traversal, ensuring that when a vertex is added, its tree connections and back-edges link properly to the portion of the graph that has already been processed.
- **Base Embedding:** The process begins with a small initial biconnected subgraph (such as a fundamental cycle) with established inside and outside face boundaries.
- **Attachment Points:** For each incoming vertex, the algorithm identifies its incident edges connecting to vertices already present in the embedded subgraph.
- **Face Admissibility & Stacks:** The algorithm uses stack-based data structures to dynamically track and manage active face segments. It checks whether the incoming edges can fit into the current face boundaries without crossing any existing lines.

Placing vertices incrementally using efficient stack structures means the algorithm avoids costly global searches, achieving an optimal $O(V)$ linear-time complexity. This makes Schmidt's algorithm significantly faster on large graphs compared to quadratic face-embedding methods like DMP.

## Steps

### DMP

1. Find any cycle in the component and draw it as the initial H. It has two faces (inside and outside).
2. Compute all fragments of H in G.
3. For each fragment, find its admissible faces.
4. If any fragment has no admissible face, stop: the graph is **non-planar**.
5. Otherwise, choose a fragment, preferring one with exactly one admissible face, since its placement is forced.
6. Pick a path through the chosen fragment between two of its attachment vertices, draw it inside an admissible face (splitting that face in two), and add it to H.
7. Repeat from step 2 until every edge is in H. Then the graph is **planar**, and the faces of H form a valid embedding.

Placing forced fragments first means the algorithm never has to undo a choice. A planar graph has at most 3v − 6 edges, so there are only O(v) path insertions, each costing O(v), which gives an overall running time of **O(v²)**.

The main drawback of DMP is that it is quadratic, so it is slower than the linear-time Left-Right test on large graphs, and it requires the graph to be split into biconnected components first.

### Schmidt's Algorithm

1. Find any cycle in the component and draw it as the initial $H$. It has two faces (inside and outside).
2. Compute all fragments of $H$ in $G$.
3. For each fragment, find its admissible faces.
4. If any fragment has no admissible face, stop: the graph is non-planar.
5. Otherwise, choose a fragment, preferring one with exactly one admissible face, since its placement is forced.
6. Pick a path through the chosen fragment between two of its attachment vertices, draw it inside an admissible face (splitting that face in two), and add it to $H$.
7. Repeat from step 2 until every edge is in $H$. Then the graph is planar, and the faces of $H$ form a valid embedding.

By adding vertices one by one using efficient stack data structures to track faces, the algorithm runs in an optimal $O(v)$ linear time.

The main drawback of Schmidt’s algorithm is its high implementation complexity, as managing active face stacks requires intricate pointer and stack logic. However, because it runs in linear time, it is significantly faster than quadratic algorithms like DMP on large graphs.

## Implementations

## DMP

The DMP Planarity Checker is implemented in Python using an adjacency-list representation of an undirected graph. Each vertex is stored as a key in a dictionary, while its neighboring vertices are stored in a set. This structure allows the program to access adjacent vertices and manage graph connections efficiently.

The implementation begins by decomposing the graph into biconnected components, also known as blocks. This process is handled by the `find_biconnected_components()` function, which uses Tarjan's Depth-First Search (DFS) technique. Dividing the graph into smaller components allows the algorithm to process each block independently, including graphs that contain articulation points or disconnected components.

For each non-trivial biconnected component, the algorithm searches for an initial cycle using the `find_cycle()` function. This cycle serves as the starting point for constructing the planar embedding. Its vertices and edges are marked as embedded, while the two sides of the cycle are represented as the initial faces. These faces provide the initial spaces into which the remaining parts of the graph can be inserted.

After establishing the initial cycle, the `get_fragments()` function identifies the remaining parts of the graph that have not yet been embedded. These fragments can be connected groups of unembedded vertices or unused edges connecting vertices that are already part of the embedding. The vertices where a fragment connects to the embedded subgraph are called contact vertices. Identifying these fragments and their contact vertices is essential for determining where each fragment can be inserted.

The algorithm then uses the `admissible_faces()` function to determine which existing faces can accommodate each fragment. A face is considered admissible when its boundary contains all the contact vertices of the fragment. The algorithm prioritizes fragments with fewer admissible faces because they have fewer possible insertion locations. If a fragment has no admissible face, the current embedding cannot be extended, and the corresponding block is classified as non-planar.

Once an admissible face has been selected, the `find_alpha_path()` function searches for a path connecting two contact vertices through the selected fragment. This path is known as an alpha-path. The `split_face()` function then inserts the path into the selected face, dividing the original face into two new faces. The embedding information is updated to include the newly inserted vertices and edges. This process is repeated until all fragments have been processed or no valid insertion is possible.

The overall process is coordinated by the `test_block_dmp()` and `is_planar_dmp()` functions. The `test_block_dmp()` function performs the DMP procedure on an individual biconnected component, while `is_planar_dmp()` manages the processing of all components and combines their results. If every block can be successfully embedded, the graph is classified as planar. Otherwise, it is classified as non-planar.

Overall, the implementation follows the main idea of the DMP algorithm by gradually constructing a planar embedding through the insertion of fragments into admissible faces. Each function handles a specific stage of the process, making the implementation easier to understand, organize, and maintain.

## Schmidt's Algorithm

The Schmidt Planarity Checker is implemented in Python using an adjacency-list representation of an undirected graph. Each vertex is stored as a key in a dictionary, while its neighboring vertices are stored in a list or set. This structure allows the program to access adjacent vertices and manage graph connections efficiently.

The implementation begins by decomposing the graph into biconnected components, also known as blocks, using Tarjan's Depth-First Search (DFS) technique via the _get_biconnected_components() method. Dividing the graph into smaller components allows the algorithm to process each block independently, ensuring that articulation points or disconnected components do not interfere with the overall planarity assessment.

For each biconnected component, the algorithm performs a Depth-First Search traversal to establish a structural vertex ordering (dfs_order). This ordering ensures that when vertices are added one by one, their tree connections and back-edges link properly to the portion of the graph that has already been processed. A small initial biconnected subgraph is chosen as the base embedding with defined face boundaries.

After establishing the base subgraph, the _test_block_schmidt() method iterates through the remaining vertices in the DFS-derived order. For each incoming vertex, it identifies all incident edges that connect to vertices already present in the embedded subgraph, acting as attachment points or contact vertices.

The algorithm then checks whether the incoming edges and the new vertex can fit into the current face boundaries without crossing any existing lines. It utilizes stack-based and set-based data structures to dynamically manage and track active face segments and stacking states. If the edges cannot be accommodated within the available face boundaries, the embedding fails, and the block is classified as non-planar.

Once a valid placement is confirmed, the new vertex and its connecting edges are embedded into the chosen face, and the face boundaries are updated. This process of adding vertices and updating face structures is repeated until all vertices in the component have been successfully processed.

The overall process is coordinated by the SchmidtPlanarityTest class, where is_planar() manages the processing of all biconnected components and combines their results. If every block can be successfully embedded, the graph is classified as planar. Otherwise, it is classified as non-planar.

Overall, the implementation follows the main idea of Schmidt’s algorithm by dynamically adding vertices one by one using efficient stack structures to manage active faces in optimal linear time. Each method handles a specific stage of the process, making the implementation organized, maintainable, and efficient.

## How to Run & Prerequisites

### DMP


Before running the DMP Planarity Checker, make sure the following requirements are met:

- **Python 3:** Installed on your computer.
- **Code Editor (Optional):** Visual Studio Code, PyCharm, or another editor for viewing and modifying the source code.
- **Project Files:** All files extracted from `DMP.zip` and placed in the same directory.
- **Third-Party Libraries:** None required, as the program uses standard Python libraries.

### Schmidt's Algorithm


Before running the Schmidt's Planarity Checker, make sure the following requirements are met:

- **Python 3:** Installed on your computer.
- **Code Editor (Optional):** Visual Studio Code, PyCharm, or another editor for viewing and modifying the source code.
- **Project Files:** All files extracted from `schmidt_project.zip` and placed in the same directory.
- **Third-Party Libraries:** None required, as the program uses standard Python libraries.

#### Running the Program

### DMP

1. **Extract the project files.** Extract `DMP.zip` into a directory on your computer.

2. **Open the project directory.** Open the extracted folder using a terminal or command prompt.

3. **Run the program.** Execute the following command:

   ```bash
   python main.py
   ```

   If the command above does not work, try:

   ```bash
   python3 main.py
   ```

4. **Enter the graph information.** Follow the instructions displayed in the terminal to enter the number of vertices, the number of edges, and the endpoints of each edge.

5. **View the result.** The program processes the graph using the DMP Planarity algorithm and displays whether the graph is planar or non-planar, along with additional embedding information when available.

### Schmidt

1. **Extract the project files.** Extract `schmidt_project.zip` into a directory on your computer.

2. **Open the project directory.** Open the extracted folder using a terminal or command prompt.

3. **Run the program.** Execute the following command:

   ```bash
   python schmidt_planarity.py
   ```

   If the command above does not work, try:

   ```bash
   python3 schmidt_planarity.py
   ```

4. **Enter the graph information.** Follow the instructions displayed in the terminal to enter the number of vertices, the number of edges, and the endpoints of each edge.

5. **View the result.** The program processes the graph using Schmidt's Planarity algorithm and displays whether the graph is planar or non-planar.

### Example Input

The following example represents a triangle graph consisting of three vertices and three edges:


### DMP

```text
Number of vertices: 3
Number of edges: 3
Edges:
0 1
1 2
2 0
```

### Schmidt's Algorithm

```text
Number of vertices: 4
Number of edges: 6
Edges:
0 1
0 2
0 3
1 2
1 3
2 3
```

#### Running the Tests

### DMP

The project also includes `test_dmp.py`, which contains test cases for the implementation. To execute the tests, run the following command from the project directory:

```bash
python -m unittest -v test_dmp.py
```

This command runs the available test cases and displays whether they pass or fail.

### Schmidt's Algorithm

```text
To test the program, run `schmidt_planarity.py` and manually enter the vertices and edges for test graphs (such as `K4`, `K5`, or `K3,3`) when prompted by the terminal to verify the results.
```

## Sample Run Result

### DMP

The following examples demonstrate how the DMP Planarity Checker processes graph inputs and displays the results.

#### Example 1: Planar Graph

**Input:**
```text
Number of vertices: 3
Number of edges: 3

Edges:
0 1
1 2
2 0
```

**Output:**
```text
Graph Analysis Result
---------------------
The graph is PLANAR.

Planar embedding:
Vertices: 0, 1, 2
Edges: (0, 1), (1, 2), (2, 0)
```

#### Example 2: Non-Planar Graph (K5)

**Input:**
```text
Number of vertices: 5
Number of edges: 10

Edges:
0 1
0 2
0 3
0 4
1 2
1 3
1 4
2 3
2 4
3 4
```

**Output:**
```text
Graph Analysis Result
---------------------
The graph is NON-PLANAR.
```

These examples illustrate how the implementation identifies whether a graph can be embedded in a plane without any edges crossing. The triangle graph is planar, while the complete graph K5 is non-planar.

### Schmidt's Algorithm
The following examples demonstrate how Schmidt's Vertex-Addition Planarity Checker processes graph inputs and displays the results.

#### Example 1: Planar Graph (K4)
**Input:**
```text
Number of vertices: 4
Number of edges: 6

Edges:
0 1
0 2
0 3
1 2
1 3
2 3
```

**Output:**
```text
Graph Analysis Result
---------------------
The graph is PLANAR.
```

#### Example 2: Non-Planar Graph (K5)
**Input:**
```text
Number of vertices: 5
Number of edges: 10

Edges:
0 1
0 2
0 3
0 4
1 2
1 3
1 4
2 3
2 4
3 4
```

**Output:**
```text
Graph Analysis Result
---------------------
The graph is NON-PLANAR.
```

#### Example 3: Non-Planar Graph ($K_{3,3}$)
**Input:**
```text
Number of vertices: 6
Number of edges: 9

Edges:
0 3
0 4
0 5
1 3
1 4
1 5
2 3
2 4
2 5
```

**Output:**
```text
Graph Analysis Result
---------------------
The graph is NON-PLANAR.
```

## Prompt History

### DMP

* Explain the Demoucron-Malgrange-Pertuiset (DMP) planarity testing algorithm, including its main concept, how it works, and the steps involved in determining whether a graph is planar.

* I want to implement the DMP planarity testing algorithm using Python. Can you help me design the program structure and identify the main functions needed?

* How can I find an initial cycle in a biconnected graph and use it to initialize the faces for the DMP path-insertion process?

* Explain how to identify fragments and their contact vertices in the currently embedded graph.

* Help me implement the admissible face selection process and explain how the algorithm chooses which fragment to process first.

* How can I implement alpha-path insertion and face splitting to update the planar embedding after inserting a path?

* Review my DMP implementation and identify possible logical errors in fragment detection, admissible face calculation, and alpha-path construction.

* Create Python unit tests for my DMP implementation using unittest. Include planar and non-planar graph examples.

* Use the triangle, tree, cycle, K4, K5, and K3,3 graphs as test cases. Explain why each graph should be classified as planar or non-planar.

* Why does running python -m unittest test_dmp.py show that no tests were run? Help me revise my test file so unittest can properly discover the test cases.

* Help me write an implementation section for the README explaining the structure and workflow of my DMP Python implementation in paragraph format.

* Create sample execution results for both planar and non-planar graphs to demonstrate how the program works.

### Schmidt's Algorithm

* What's a good planar algorithm?

* Explain Schmidt's algorithm, including how it works and the steps to check if a graph is planar.

* I want to code Schmidt's algorithm in Python. Can you help me plan the program and list the main functions?

* How can I set up the graph and get inputs from the terminal?

* Explain how to check if a graph is planar when adding vertices.

* Help me write and fix the functions that track edges and check planarity.

* How can I fix problems with running the script and getting terminal inputs?

* Review my Schmidt code and help me find any mistakes in how it checks planarity.

* Write Python unit tests with planar and non-planar graph examples.

* Use $K_4$, $K_5$, and $K_{3,3}$ as test graphs and explain why each is planar or non-planar.

* Why is my script running automatically instead of waiting for input? Help me fix PowerShell path issues (.\schmidt_planarity.py).

* Help me write a simple README section that explains how the Python code works.

* Show sample inputs and outputs for running the program with planar and non-planar graphs.
