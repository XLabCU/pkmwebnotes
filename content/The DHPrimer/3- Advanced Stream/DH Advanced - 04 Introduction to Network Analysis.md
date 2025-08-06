---
title: "04: Introduction to Network Analysis"
created: 2025-08-05T12:57:24.739Z
tags: [advanced]
---
# DH Advanced - 04: Introduction to Network Analysis

Many phenomena in the humanities involve relationships: characters interacting in a novel, scholars citing each other's work, historical figures corresponding, locations connected by trade routes, concepts linked in texts. **Network Analysis** (also known as Graph Theory) provides a powerful mathematical and visual framework for modeling and analyzing these relational structures.

**Core Concepts:**

*   **Network/Graph:** A collection of entities and the connections between them.
    *   **Nodes (or Vertices):** Represent the entities (e.g., people, places, texts, concepts).
    *   **Edges (or Links/Ties):** Represent the connections or relationships between nodes (e.g., friendship, citation, co-occurrence, correspondence).
*   **Types of Graphs:**
    *   **Undirected:** Edges represent symmetric relationships (e.g., Facebook friends, co-authorship). If A is connected to B, B is connected to A.
    *   **Directed (Digraph):** Edges have a direction, representing asymmetric relationships (e.g., Twitter follower, citation, hyperlink, flow of goods). A connection from A to B is distinct from B to A.
    *   **Weighted:** Edges have numerical values (weights) representing the strength, cost, or frequency of the connection (e.g., number of letters exchanged, strength of similarity, distance).
    *   **Multigraph:** Allows multiple edges (possibly with different meanings) between the same pair of nodes.
*   **Attributes:** Nodes and edges can have associated properties or metadata (e.g., a 'Person' node might have an 'occupation' attribute; an edge might have a 'date' attribute).

**Why Use Network Analysis in DH?**

*   **Visualize Relationships:** Make complex relational patterns intuitive and visible.
*   **Identify Key Actors/Items:** Find central or influential nodes using centrality measures.
*   **Discover Communities:** Identify clusters or groups of densely connected nodes.
*   **Analyze Structure:** Understand the overall shape and properties of the relational system (e.g., density, path lengths).
*   **Model Dynamic Processes:** Simulate flows or changes within the network over time.

**Implementation with `NetworkX`:**

`NetworkX` is the standard Python library for creating, manipulating, and studying the structure, dynamics, and functions of complex networks.

**Installation:**

`pip install networkx matplotlib`
*(Matplotlib is needed for basic visualization)*

**Creating a Simple Network:**

Let's model a small social network based on fictional character interactions.

```python
import networkx as nx
import matplotlib.pyplot as plt # For visualization

# 1. Create an empty graph object
#    Use nx.Graph() for undirected graphs
#    Use nx.DiGraph() for directed graphs
G = nx.Graph() # Undirected graph for simple co-occurrence/interaction

# 2. Add Nodes
#    Add nodes one by one
G.add_node("Alice")
G.add_node("Bob")
#    Add multiple nodes from a list
G.add_nodes_from(["Charlie", "David", "Eve"])

#    Add nodes with attributes
G.add_node("Frank", role="Mentor")
G.add_node("Grace", role="Student")

print(f"Nodes in graph: {G.nodes()}")
# Access node attributes
print(f"Attribute for Frank: {G.nodes['Frank']}")

# 3. Add Edges
#    Add edges one by one (between existing or new nodes)
G.add_edge("Alice", "Bob")
#    Add multiple edges from a list of tuples
G.add_edges_from([("Alice", "Charlie"), ("Bob", "Charlie"), ("Bob", "David")])
G.add_edges_from([("David", "Eve"), ("Charlie", "Eve")])
G.add_edges_from([("Frank", "Grace"), ("Alice", "Frank")]) # Frank mentors Grace, knows Alice

#    Add edges with attributes (e.g., weight)
G.add_edge("Charlie", "David", weight=3) # Maybe they interacted 3 times
G.add_edge("Eve", "Frank", weight=1, relationship="Acquaintance")

print(f"\nEdges in graph: {G.edges()}")
# Access edge attributes
print(f"Attributes for edge Eve-Frank: {G.edges[('Eve', 'Frank')]}") # Use tuple for undirected edge lookup

# 4. Basic Graph Info
print(f"\nNumber of nodes: {G.number_of_nodes()}")
print(f"Number of edges: {G.number_of_edges()}")
# List neighbors of a node
print(f"Neighbors of Bob: {list(G.neighbors('Bob'))}")
# Degree of a node (number of connections)
print(f"Degree of Charlie: {G.degree('Charlie')}")

# 5. Basic Visualization (using Matplotlib)
plt.figure(figsize=(8, 6)) # Set figure size
# nx.draw uses matplotlib to draw the graph
# Parameters:
#   with_labels=True: Show node names
#   node_color='skyblue': Color of nodes
#   node_size=1500: Size of nodes
#   edge_color='gray': Color of edges
#   font_size=10: Size of labels
#   pos=nx.spring_layout(G): Algorithm to position nodes (others: circular, random, etc.)
pos = nx.spring_layout(G, seed=42) # Use a seed for reproducible layout
nx.draw(G, pos, with_labels=True, node_color='skyblue', node_size=1200, edge_color='gray', font_size=10, font_weight='bold')

# Optionally draw edge weights (if they exist and are simple enough)
edge_labels = nx.get_edge_attributes(G, 'weight')
if edge_labels:
    nx.draw_networkx_edge_labels(G, pos, edge_labels=edge_labels)

plt.title("Simple Character Interaction Network")
plt.show()
```

**Reading/Writing Graphs:**

NetworkX supports various file formats:

*   **Edge List:** Simple text file listing pairs of connected nodes, one pair per line.
*   **Adjacency List:** Text file where each line starts with a node, followed by its neighbors.
*   **GEXF, GraphML, JSON:** More complex formats that preserve node and edge attributes.

```python
# Example: Save to GEXF (good for attributes, readable by Gephi/other tools)
try:
    output_gexf = "simple_network.gexf"
    nx.write_gexf(G, output_gexf)
    print(f"\nSaved graph to '{output_gexf}'")

    # Example: Read from GEXF
    # G_loaded = nx.read_gexf(output_gexf)
    # print(f"Loaded graph has {G_loaded.number_of_nodes()} nodes.")

except Exception as e:
    print(f"\nError saving/reading graph file: {e}")

```

**Data Sources for DH Networks:**

*   **Literary Texts:** Character co-occurrence in scenes/chapters, dialogue interactions. (Requires NER/parsing).
*   **Historical Documents:** Correspondence networks (sender-receiver), co-signatories on documents, relationships mentioned in biographies. (Requires NER, careful data modeling).
*   **Bibliographic Data:** Citation networks (papers citing papers), co-authorship networks.
*   **Social Media:** Follower/friend relationships, retweet/mention networks. (Requires API access).
*   **Database Records:** Linking individuals based on shared events, locations, or family ties.

This introduction covers the fundamental concepts and tools for creating basic networks. The real power comes from analyzing their structure using various metrics and algorithms.

➡️ **Next Step:** [[DH Advanced - 05 Network Metrics and Community Detection]]

---

*Back to [[Welcome to the DHP]] | [[DH Advanced - Start Here]]*