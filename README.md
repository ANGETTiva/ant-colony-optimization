# 🐜 Traveling Salesman Problem with Ant Colony Optimization

![Python](https://img.shields.io/badge/Python-3.8%2B-blue?logo=python)
![NumPy](https://img.shields.io/badge/NumPy-1.21%2B-013243?logo=numpy)
![Matplotlib](https://img.shields.io/badge/Matplotlib-3.4%2B-orange)
![License](https://img.shields.io/badge/License-MIT-green)

A Python implementation of the **Ant Colony Optimization (ACO)** algorithm to solve a constrained **Traveling Salesman Problem (TSP)** on a partial graph. The program finds the shortest path from node **H** to node **D**, with a mandatory visit to node **#**.

---

## 📌 Problem Description

Given the following undirected weighted graph:

```
        #
       /|\
      3 2 5
     /  |  \
    A   C   G
    |\ /|   |\
    6 X 4   1 3
    |/ \|   |  \
    B   F   E - H
    |    \ /|   |
    9     2 7   9
    |       |   |
    +---8---D---+
```

| Edge   | Weight | Edge   | Weight |
|--------|--------|--------|--------|
| # — A  | 3      | C — F  | 4      |
| # — C  | 2      | D — E  | 7      |
| # — G  | 5      | D — H  | 9      |
| A — B  | 9      | E — F  | 2      |
| A — C  | 6      | E — G  | 1      |
| B — D  | 8      | E — H  | 1      |
|        |        | G — H  | 3      |

**Objective:** Find the shortest route from **H → D** that **must pass through node #**.

---

## 🧠 Algorithm Overview

This implementation uses **Ant Colony Optimization (ACO)**, specifically the **Ant-Cycle** variant (the best-performing version of the original Ant System).

### Key Steps

```
1. Build distance matrix from graph edges
2. Apply Floyd-Warshall to compute all-pairs shortest paths
3. Initialize pheromone matrix uniformly
4. For each iteration:
   a. Each ant builds a route from START to END
   b. Route must include all mandatory nodes
   c. Next node selected probabilistically:
      P(i→j) ∝ τ(i,j)^α × η(i,j)^β
   d. Update pheromones (evaporation + deposit)
5. Return best route found
```

### Probability Formula

$$P(i \to j) = \frac{\tau_{ij}^\alpha \cdot \eta_{ij}^\beta}{\sum_{k \notin visited} \tau_{ik}^\alpha \cdot \eta_{ik}^\beta}$$

Where:
- `τ (tau)` = pheromone level on edge (i, j)
- `η (eta)` = visibility = 1 / distance(i, j)
- `α` = pheromone weight
- `β` = visibility weight

### Pheromone Update

$$\tau_{ij}(t+1) = (1 - \rho) \cdot \tau_{ij}(t) + \sum_{k=1}^{m} \Delta\tau_{ij}^k$$

$$\Delta\tau_{ij}^k = \frac{Q}{L^k} \quad \text{if ant } k \text{ used edge } (i,j), \text{ else } 0$$

---

## ✨ Features

- **Floyd-Warshall preprocessing** — handles partial graphs by computing all-pairs shortest paths, ensuring no ant gets stuck
- **Mandatory node constraint** — ants are required to visit specified nodes before reaching the destination
- **Interactive input** — user can specify start node, end node, and mandatory waypoints at runtime
- **Dual visualization** — convergence graph + graph route visualization with color-coded nodes
- **Reproducible results** — fixed random seed for consistent output
- **Auto-save output** — saves visualization as `hasil_aco_tsp.png`

---

## 🚀 Getting Started

### Prerequisites

```bash
pip install numpy matplotlib
```

> All libraries (`numpy`, `matplotlib`, `random`) are pre-installed in **Google Colab** — no installation needed.

### Running the Program

**Option 1: Local (terminal)**
```bash
python AntColony_TSP.py
```

**Option 2: Google Colab**
1. Open [colab.research.google.com](https://colab.research.google.com)
2. Create a new notebook
3. Copy-paste the entire code into a cell
4. Click ▶ Run
5. Fill in the prompts when they appear

### Input Prompts

```
Masukkan node awal    : H
Masukkan node akhir   : D
Node wajib dilewati   : #
```

### Expected Output

```
============================================================
  HASIL RUTE TERPENDEK
============================================================
  Rute   : H -> E -> G -> # -> C -> F -> D
  Jarak  : 22.00

  Detail segmen:
    H -> E  : 1
    E -> G  : 1
    G -> #  : 5
    # -> C  : 2
    C -> F  : 4
    F -> D  : 9
============================================================
```

---

## 📊 Visualization Output

The program generates two plots side by side:

| Plot | Description |
|------|-------------|
| **Convergence Graph** | Shows how the best distance improves over iterations |
| **Route Visualization** | Displays the graph with the best route highlighted in blue arrows |

### Node Color Legend

| Color | Meaning |
|-------|---------|
| 🟢 Green | Start node |
| 🟠 Orange | End node |
| 🔴 Red | Mandatory node (#) |
| 🔵 Blue | Regular node |

---

## ⚙️ Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `N_ANTS`  | 20      | Number of ants per iteration |
| `N_ITER`  | 150     | Maximum number of iterations |
| `ALPHA`   | 1.0     | Pheromone weight (higher = follow pheromone more) |
| `BETA`    | 2.0     | Visibility weight (higher = prefer shorter edges) |
| `RHO`     | 0.5     | Pheromone evaporation rate (0–1) |
| `Q`       | 1.0     | Pheromone deposit constant |

You can tune these values at the top of the script to experiment with different results.

---

## 📁 Project Structure

```
📦 AntColony-TSP
 ┣ 📄 AntColony_TSP.py      ← Main program
 ┣ 📄 README.md             ← This file
 ┗ 📄 hasil_aco_tsp.png     ← Output visualization (auto-generated)
```

---

## 🔍 Why Floyd-Warshall?

Unlike standard TSP benchmarks (e.g., TSPLIB) which use **complete graphs** where every node is directly connected, this problem uses a **partial graph** where not all nodes have direct edges.

Without Floyd-Warshall:
- Ants would get **stuck** at nodes with no direct path to the destination
- Many routes would be **invalid or incomplete**

With Floyd-Warshall:
- All-pairs shortest paths are **pre-computed** (O(n³))
- ACO treats the graph as **virtually complete**
- Ants always find valid routes using **real underlying paths**

---

## 📖 References

- Dorigo, M. (1992). *Optimization, learning and natural algorithms*. Doctoral dissertation, Politecnico di Milano.
- Thirugnanasambandam, K., et al. (2019). *Experimental Analysis of Ant System on Travelling Salesman Problem Dataset TSPLIB*. EAI Endorsed Transactions on Pervasive Health and Technology, 5(19).
- Dorigo, M., & Gambardella, L.M. (1997). *Ant Colony System: A cooperative learning approach to the traveling salesman problem*. IEEE Transactions on Evolutionary Computation, 1(1), 53–66.

---

## 👤 Author

> Created as an assignment for solving TSP with ACO on a constrained partial graph.

---

## 📄 License

This project is licensed under the MIT License.
