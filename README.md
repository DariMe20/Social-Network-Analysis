# Analyzing Urban Traffic Networks using SNA — Cluj-Napoca Road System

Social Network Analysis (SNA) applied to the real road network of **Cluj-Napoca, Romania**. The project models the city's streets as a graph, computes centrality measures to find the most important intersections and likely traffic bottlenecks, and runs community detection to see whether the network's structural divisions match the city's official neighborhoods (*cartiere*).

> Full analysis with all plots: [`SNA_trafic_application.ipynb`](./SNA_trafic_application.ipynb) · [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/DariMe20/Social-Network-Analysis/blob/main/Project_Traffic_SNA/SNA_trafic_application.ipynb)

## Overview

The road network is downloaded from OpenStreetMap via OSMnx and built as a directed, weighted graph where **nodes are intersections / dead-ends** and **edges are drivable road segments**. The analysis then proceeds in four stages:

1. **Network construction & basic structure** — degree distribution and connectivity.
2. **Centrality analysis** — degree, closeness, and betweenness, computed city-wide and zoomed into the 2 km city center.
3. **Centrality correlations** — how the three measures relate to one another.
4. **Community detection** — Greedy Modularity, Label Propagation, and Louvain, compared against official neighborhoods.

## Tech stack

`OSMnx` · `NetworkX` · `python-louvain` · `pandas` · `NumPy` · `SciPy` · `Matplotlib`

## The network

The raw drivable street network of Cluj-Napoca, with intersections as nodes and road segments as edges.

![Cluj-Napoca road network](images/road_network.png)

| Property | Value |
|---|---|
| Nodes (intersections) | 3,244 |
| Edges (road segments) | 7,313 |
| Average node degree | 4.51 |

Most intersections connect 3–4 streets, which is typical for urban road grids; the high-degree nodes are the major traffic hubs.

![Degree distribution](images/degree_distribution.png)

## Centrality measures

Three measures capture different notions of "importance":

- **Degree** — local connectivity (how many roads meet at an intersection).
- **Closeness** — accessibility (how quickly a node reaches the rest of the network).
- **Betweenness** — bottleneck potential (how often a node sits on shortest paths). Approximated with `k=500` sampled sources for tractability.

| Degree | Closeness | Betweenness |
|:---:|:---:|:---:|
| ![Degree centrality](images/degree_centrality.png) | ![Closeness centrality](images/closeness_centrality.png) | ![Betweenness centrality](images/betweenness_centrality.png) |

The highest-betweenness intersections cluster on major arteries such as **Strada Paris, Dorobanților, and 21 Decembrie** — the points where disruption would most affect city-wide flow.

### Correlations between measures

![Centrality correlations](images/centrality_correlations.png)

| Pair | Pearson r | Interpretation |
|---|---|---|
| Degree – Closeness | 0.143 | weak positive |
| Betweenness – Closeness | 0.375 | moderate positive |
| Degree – Betweenness | 0.276 | weak positive |

Having many connected roads doesn't strongly predict being a critical connector — accessibility (closeness) is the better signal for bottleneck risk.

## City center (2 km radius)

A subgraph around the center point `(46.7712, 23.6236)` isolates the urban core (**52 nodes, 108 edges**) and re-runs the centrality measures to highlight the most critical and most accessible downtown intersections.

![City center betweenness centrality](images/city_center_betweenness.png)

## Community detection

Three algorithms partition the network into communities:

| Algorithm | Communities detected |
|---|---|
| Greedy Modularity | 38 |
| Louvain | 40 |
| Label Propagation | 1,025 |

Greedy Modularity and Louvain agree on a small number of larger communities, while Label Propagation fragments into many small ones.

![Louvain communities](images/community_louvain.png)

The Louvain partition plotted side-by-side with the official neighborhoods compares structural vs. administrative divisions of the city:

![Official neighborhoods vs detected Louvain communities](images/neighborhoods_vs_louvain.png)

## Running it

```bash
pip install osmnx networkx matplotlib pandas python-louvain scipy
```

Then open the notebook and run all cells, or use the Colab badge above. OSMnx downloads live OpenStreetMap data, so exact node/edge counts may shift slightly as the map is updated.

## Repository structure

```
Project_Traffic_SNA/
├── SNA_trafic_application.ipynb   # full analysis
├── images/                        # exported plots used in this README
└── README.md
```
