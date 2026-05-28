# Financial Fraud Detection Using Graph Data Science
### Unmasking Mobile Money Fraud with Neo4j, Cypher, and Graph Algorithms

![Project Architecture](Graph_Science_vs._Financial_Fraud.png)

---

## Overview

Traditional fraud detection relies on rule-based thresholds that sophisticated fraudsters evade trivially. The PaySim dataset exposes this failure directly: its built-in flagging system caught only **16 out of 8,213 fraud transactions — a 99.81% evasion rate — missing over $12 billion in fraudulent transfers**.

This project re-frames fraud detection as a **graph problem**. Rather than scoring individual transactions in isolation, we load the full PaySim dataset into a Neo4j bipartite property graph and apply Cypher queries and Graph Data Science algorithms to surface fraud patterns that are only visible at the network level.

---

## Team

**Spring 2026 Capstone — Equal Contribution**

- Ruvarashe Mabika
- Chidochashe Makanga
- Arnold Muzarurwi

---

## Dataset

**PaySim Synthetic Financial Dataset** — a peer-reviewed simulation of mobile money transactions modelled on real-world logs from a multinational provider operating across 14+ countries.

| Property | Value |
|---|---|
| Total transactions | 6,362,620 |
| Simulation period | 744 hours (31 days) |
| Transaction types | CASH_IN, CASH_OUT, PAYMENT, TRANSFER, DEBIT |
| Unique client accounts | 9,073,900 |
| Fraud transactions | 8,213 (0.13%) |
| Total fraud volume | $12.06 billion |
| Graph nodes loaded | 15,436,520 |
| Graph relationships | 12,725,240 |

**Full dataset:** [PaySim on Kaggle](https://www.kaggle.com/datasets/ealaxi/paysim1)

**Citation:** Lopez-Rojas, E.A., Elmir, A., & Axelsson, S. (2016). *PaySim: A financial mobile money simulator for fraud detection.* 28th European Modelling and Simulation Symposium (EMSS), Larnaca, Cyprus.

---

## Graph Data Model

Transactions are promoted to **first-class nodes** rather than edges, enabling the GDS library to reason about individual transactions across multi-hop paths.

```
(Client) -[:INITIATED]-> (Transaction) -[:CREDITED]-> (Client)
```

Every client-to-client path traverses exactly **two hops** through a Transaction node, keeping all transaction properties accessible throughout.

---

## Methodology

### Part I — Exploratory Data Analysis (12 Cypher Queries)
Baseline profiling covering graph structure verification, transaction type distributions, fraud vs. legitimate volume, account out-degree distribution, and balance depletion patterns across all five transaction types.

### Part II — Advanced Pattern Detection (6 Cypher Queries)

| Query | Objective |
|---|---|
| Q1 | Transaction type risk profiles — fraud rate and average amount by type |
| Q2 | Money mule sink accounts — clients receiving from 5+ distinct sources, never sending |
| Q3 | Circular trading ring investigation — six independent structural tests |
| Q4 | Fraud ring volume — monetary value and fraud leg count per detected ring |
| Q5 | The actual fraud mechanism — evasion pattern, directional flow, detection failure |
| Q6 | Time-based fraud clustering — coordinated burst window analysis |

### Part III — Graph Data Science Algorithms (4 Algorithms)

| Algorithm | What It Measures |
|---|---|
| Weakly Connected Components (WCC) | Structural isolation — accounts disconnected from the broader network |
| Louvain Community Detection | Tight sub-communities transacting almost exclusively with each other |
| PageRank Centrality | Network influence — accounts most central to capital flow |
| In-Degree Centrality | Sink detection — accounts receiving from the highest number of distinct sources |

---

## Key Findings

| Finding | Detail |
|---|---|
| Rule-based detection failure | 99.81% of fraud (8,197 transactions, $12B+) evades the built-in threshold system |
| No circular trading rings | Six independent structural tests confirm zero 3-hop cycles in the full dataset |
| One-and-done fraud pattern | Every fraud account committed exactly one transaction — deliberately staying below detection thresholds |
| Louvain unsupervised detection | 2,660 fraud pairs identified without consulting the isFraud label (~65% unsupervised recall) |
| PageRank negative result | Zero fraud accounts appear in the top 20 PageRank scores — high centrality signals legitimacy, not fraud |
| Three-method convergence | Q2 Cypher, PageRank, and In-Degree all surface the same sink accounts independently |

---

## Tools & Environment

| Tool | Purpose |
|---|---|
| Neo4j Desktop 2.1.3+ | Graph database engine and Cypher query browser |
| Neo4j GDS Library 2.26 | In-memory graph projections and algorithm execution |
| Python `graphdatascience` 1.20 | Neo4j Python client for Jupyter integration |
| Pandas / NumPy | Data manipulation and batch CSV ingestion |
| Matplotlib | Visualisation |
| Jupyter Notebook | Interactive analysis and submission format |

---

## Running the Notebook

1. Install [Neo4j Desktop](https://neo4j.com/download/) and create a database named `FraudAnalytics`.
2. Install the GDS Library plugin via the Neo4j Desktop plugin manager.
3. Download the full PaySim dataset from [Kaggle](https://www.kaggle.com/datasets/ealaxi/paysim1) and place it in your Neo4j import directory.
4. Update the connection credentials in the notebook Setup cell to match your local Neo4j instance.
5. Run all cells sequentially — data ingestion must complete before the analysis phases.
