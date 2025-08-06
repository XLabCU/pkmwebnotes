---
title: "05: Network Metrics and Community Detection"
created: 2025-08-05T12:57:24.739Z
tags: [advanced]
---
# DH Advanced - 05: Network Metrics and Community Detection

Creating and visualizing a network is often just the first step. To gain deeper insights into its structure and the roles of individual nodes, we analyze **network metrics**. Furthermore, identifying groups or **communities** of densely connected nodes can reveal underlying structures or functional units within the network.

**1. Network Metrics (Centrality Measures):**

Centrality metrics quantify the "importance" or "influence" of a node within the network, though "importance" can mean different things. Common measures include:

*   **Degree Centrality:** Measures the number of direct connections a node has. In a directed graph, we distinguish between *in-degree* (connections pointing *to* the node) and *out-degree* (connections originating *from* the node).
    *   *Interpretation:* Nodes with high degree are "hubs" or highly connected locally.
*   **Betweenness Centrality:** Measures how often a node lies on the shortest paths between other pairs of nodes in the network.
    *   *Interpretation:* Nodes with high betweenness act as "bridges" or "brokers," controlling information flow between different parts of the network. Removing them can fragment the network.
*   **Closeness Centrality:** Measures the average shortest distance from a node to all *other reachable* nodes in the network. (Calculated as `1 / average_shortest_path_length`).
    *   *Interpretation:* Nodes with high closeness can reach other nodes quickly, suggesting efficient information dissemination from that node.
*   **Eigenvector Centrality:** Measures a node's influence based on the idea that connections to other high-scoring nodes contribute more than connections to low-scoring nodes (like Google's PageRank).
    *   *Interpretation:* Identifies nodes connected to other influential nodes, indicating influence within important clusters.

**Implementation with NetworkX:**

Let's use the graph created in the previous notebook and calculate these metrics.

```python
import networkx as nx
import matplotlib.pyplot as plt
import pandas as pd # For displaying metrics nicely

# --- Load or Recreate the Graph from Previous Note ---
# (Assuming G exists from the previous execution context or reloading it)
# If not, uncomment and run the creation code from DH Advanced - 04 here.
# Example recreation snippet:
G = nx.Graph()
G.add_edges_from([("Alice", "Bob"), ("Alice", "Charlie"), ("Bob", "Charlie"),
                  ("Bob", "David"), ("David", "Eve"), ("Charlie", "Eve"),
                  ("Frank", "Grace"), ("Alice", "Frank"), ("Charlie", "David"),
                  ("Eve", "Frank")])
G.add_node("Frank", role="Mentor")
G.add_node("Grace", role="Student")
# Add weights if needed for weighted metrics later
G.edges[('Charlie', 'David')]['weight'] = 3
G.edges[('Eve', 'Frank')]['weight'] = 1
# --- End Recreation Snippet ---

print("--- Calculating Network Metrics ---")

# 1. Degree Centrality
#    Returns a dictionary {node: degree}
degree_dict = dict(G.degree())
#    nx.degree_centrality(G) normalizes by (N-1)
degree_centrality = nx.degree_centrality(G)

# 2. Betweenness Centrality
#    Returns a dictionary {node: betweenness}
betweenness_centrality = nx.betweenness_centrality(G) # Normalized by default

# 3. Closeness Centrality
#    Returns a dictionary {node: closeness}
closeness_centrality = nx.closeness_centrality(G)

# 4. Eigenvector Centrality
#    Returns a dictionary {node: eigenvector centrality}
#    May fail on graphs with multiple connected components; handle with try-except
try:
    eigenvector_centrality = nx.eigenvector_centrality(G, max_iter=1000) # Increase max_iter if needed
except nx.PowerIterationFailedConvergence:
    print("Eigenvector centrality failed to converge.")
    eigenvector_centrality = {node: 0 for node in G.nodes()} # Assign default value

# --- Display Metrics ---
metrics_df = pd.DataFrame({
    'Degree': pd.Series(degree_dict),
    'Degree Centrality (Norm)': pd.Series(degree_centrality),
    'Betweenness Centrality': pd.Series(betweenness_centrality),
    'Closeness Centrality': pd.Series(closeness_centrality),
    'Eigenvector Centrality': pd.Series(eigenvector_centrality)
})

# Sort by a metric, e.g., Betweenness
print(metrics_df.sort_values(by='Betweenness Centrality', ascending=False))

# --- Visualize with Node Size based on a Metric ---
plt.figure(figsize=(10, 8))
pos = nx.spring_layout(G, seed=42) # Consistent layout

# Use Betweenness Centrality to scale node size (multiply for visibility)
node_sizes = [v * 5000 + 500 for v in betweenness_centrality.values()] # Adjust scaling factor

nx.draw(G, pos, with_labels=True, node_color='skyblue', node_size=node_sizes,
        edge_color='gray', font_size=10, font_weight='bold')

edge_labels = nx.get_edge_attributes(G, 'weight')
if edge_labels:
    nx.draw_networkx_edge_labels(G, pos, edge_labels=edge_labels)

plt.title("Network Visualization (Node Size by Betweenness Centrality)")
plt.show()
```

**Interpretation:** In our example, nodes like 'Alice', 'Bob', and 'Charlie' might have high degrees. 'Charlie' and 'Eve' might have higher betweenness centrality as they bridge connections between different parts of the small network.

**2. Community Detection:**

Community detection algorithms aim to partition the network into groups (communities) where nodes within a group are more densely connected to each other than to nodes outside the group. This helps identify clusters, factions, or functional modules.

*   **Modularity:** A common metric used to evaluate the quality of a partition. It measures the density of links *within* communities compared to what would be expected in a random network with the same degree distribution. Higher modularity generally indicates a better community structure.
*   **Common Algorithms:**
    *   **Louvain Algorithm:** A popular and efficient greedy algorithm based on optimizing modularity. Often a good starting point.
    *   **Girvan-Newman Algorithm:** An older algorithm based on iteratively removing edges with high betweenness centrality to break the network into communities (computationally expensive for large networks).
    *   Label Propagation, Infomap, etc.

**Implementation with NetworkX (Louvain via community library):**

The Louvain algorithm isn't directly in NetworkX core but is available in the separate `python-louvain` (also known as `community`) library.

**Installation:**
`pip install python-louvain`

```python
# Check if graph is connected (community detection works best on connected components)
if nx.is_connected(G):
    try:
        import community as community_louvain # pip install python-louvain

        print("\n--- Performing Community Detection (Louvain) ---")
        # Compute the best partition using the Louvain algorithm
        partition = community_louvain.best_partition(G) # Returns a dict {node: community_id}

        # Print the partition results
        print("Node Partitions:")
        print(partition)

        # Create a mapping for visualization colors
        community_ids = set(partition.values())
        num_communities = len(community_ids)
        # Generate colors using a colormap
        cmap = plt.get_cmap('viridis', num_communities)
        node_colors = [cmap(partition[node] / num_communities) for node in G.nodes()]

        # Visualize the graph with nodes colored by community
        plt.figure(figsize=(10, 8))
        pos = nx.spring_layout(G, seed=42) # Use the same layout

        nx.draw(G, pos, with_labels=True, node_color=node_colors, node_size=1200,
                edge_color='gray', font_size=10, font_weight='bold')

        edge_labels = nx.get_edge_attributes(G, 'weight')
        if edge_labels:
            nx.draw_networkx_edge_labels(G, pos, edge_labels=edge_labels)

        plt.title(f"Network Visualization (Nodes Colored by Louvain Community - {num_communities} communities found)")
        plt.show()

        # Calculate Modularity (optional)
        modularity_score = community_louvain.modularity(partition, G)
        print(f"\nModularity of the partition: {modularity_score:.4f}")

    except ImportError:
        print("\n'python-louvain' library not found. Cannot perform Louvain community detection.")
        print("Install it using: pip install python-louvain")
    except Exception as e:
        print(f"\nAn error occurred during community detection: {e}")
else:
    # If graph is not connected, run detection on each component separately
    print("\nGraph is not connected. Community detection should ideally be run on each connected component.")
    # Example: Find components and potentially run partition on the largest one
    # components = list(nx.connected_components(G))
    # largest_component = max(components, key=len)
    # subgraph = G.subgraph(largest_component)
    # partition = community_louvain.best_partition(subgraph)
    # ... (rest of the visualization/analysis for the subgraph) ...

```

**Interpretation:** The visualization now colors nodes based on their assigned community ID. In our small example, we might see distinct groups emerge (e.g., {Alice, Bob, Charlie, David, Eve} vs. {Frank, Grace}). The modularity score gives a quantitative measure of how well-defined this separation is.

**Applications in DH:**

*   Identifying influential historical figures (high centrality).
*   Finding bridges between scholarly communities (betweenness centrality in citation networks).
*   Detecting thematic clusters in co-occurrence networks derived from texts.
*   Analyzing social structures in literary or historical settings.

Network metrics and community detection provide analytical depth beyond simple visualization, allowing for quantitative assessment of node roles and group structures within complex relational systems.

➡️ **Next Step:** [[DH Advanced - 06 Geocoding - Turning Place Names into Coordinates]]

---

*Back to [[Welcome to the DHP]] | [[DH Advanced - Start Here]]*