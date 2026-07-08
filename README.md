# Graph Neural Networks for Metabolic Pathway Analysis

<p align="center">
<b>
A From-Scratch Graph Convolutional Network Framework for Enzyme Reaction Participation Prediction in KEGG-Derived Biological Networks
</b>
</p>

<p align="center">
Research Timeline: <b>Fall 2025</b>
</p>

---

<p align="center">

![Python](https://img.shields.io/badge/Python-3.12-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-2.9.0-red)
![Domain](https://img.shields.io/badge/Domain-Bioinformatics-green)
![Model](https://img.shields.io/badge/Model-Graph%20Neural%20Network-orange)

</p>

---

# Abstract

This repository presents a research-oriented implementation of a Graph Neural Network (GNN) framework for metabolic pathway analysis and enzyme reaction participation prediction.

The objective of this study is to construct a biological graph from KEGG metabolic pathway information and investigate whether Graph Convolutional Networks (GCNs) can learn meaningful structural and pathway-level representations for identifying enzymes involved in multiple biochemical reactions.

A two-layer GCN model was implemented **from scratch using PyTorch tensor operations**, without relying on specialized graph learning frameworks including:

* PyTorch Geometric
* DGL
* Spektral

The project demonstrates the application of graph-based deep learning methods to biological network analysis.

---

# Research Objective

Metabolic pathways form complex biological networks where enzymes participate in interconnected biochemical processes.

This project investigates the following research question:

> Can Graph Neural Networks learn enzyme connectivity patterns and pathway information to predict enzymes involved in multiple biochemical reactions?

The task is formulated as a binary node classification problem.

| Label | Biological Interpretation                 |
| ----- | ----------------------------------------- |
| 0     | Enzyme participates in one reaction       |
| 1     | Enzyme participates in multiple reactions |

Multi-reaction enzymes are biologically important because they may connect multiple metabolic processes and contribute to pathway regulation.

---

# Research Timeline

**Fall 2025**

This project was conducted as part of a Bioinformatics research study focusing on the integration of computational biology and graph-based machine learning.

---

# Methodology Overview

The complete workflow consists of:

```
KEGG KGML Pathways

        ↓

Biological Graph Construction

        ↓

Node Feature Engineering

        ↓

GCN Representation Learning

        ↓

Binary Enzyme Classification

        ↓

Performance Evaluation

```

---

# Biological Dataset

## Source Database

The biological network was constructed using:

**KEGG (Kyoto Encyclopedia of Genes and Genomes)**

Organism:

```
Saccharomyces cerevisiae (yeast)
```

KGML pathway files were retrieved and processed to extract:

* Enzyme nodes
* KEGG biological relationships
* Pathway membership information

---

# Selected Metabolic Pathways

Six KEGG pathways were included:

| KEGG ID | Pathway                      |
| ------- | ---------------------------- |
| 00010   | Glycolysis / Gluconeogenesis |
| 00020   | Citrate Cycle (TCA Cycle)    |
| 00030   | Pentose Phosphate Pathway    |
| 00620   | Pyruvate Metabolism          |
| 01200   | Carbon Metabolism            |
| 01230   | Biosynthesis of Amino Acids  |

---

# Graph Construction

The metabolic network was represented as:

[
G=(V,E)
]

where:

* **V** represents enzyme nodes
* **E** represents KEGG-derived biological relationships

Only enzyme-related KEGG gene entries were retained.

Compounds and non-enzyme entries were excluded.

The final graph was converted into an undirected representation for GCN message passing.

---

# Graph Statistics

| Property             |     Value |
| -------------------- | --------: |
| Enzyme nodes         |       222 |
| Directed relations   |      1180 |
| Undirected edges     |       590 |
| Node features        |         8 |
| Class 0 nodes        |       126 |
| Class 1 nodes        |        96 |
| Isolated nodes       |        98 |
| Connected components |        99 |
| Largest component    | 124 nodes |
| Edge density         |     0.024 |

The resulting network reflects the sparse and modular characteristics of biological systems.

---

# Node Feature Engineering

Each enzyme node is represented by an 8-dimensional feature vector:

| Feature     | Description                   |
| ----------- | ----------------------------- |
| col00       | log1p node degree             |
| col01-col06 | Pathway membership indicators |
| col07       | Bias constant                 |

Feature preprocessing:

* Degree transformation using `log1p`
* Feature normalization
* Zero-centered scaling

Feature-label correlation analysis:

| Feature       |    Correlation |
| ------------- | -------------: |
| pathway_01200 |         +0.348 |
| pathway_00620 |         -0.204 |
| Bias          | No correlation |

---

# Graph Neural Network Model

A Graph Convolutional Network was implemented from scratch.

Architecture:

```
Input Node Features

        ↓

GCN Layer
(64 hidden dimensions)

        ↓

ReLU Activation

        ↓

Dropout (0.35)

        ↓

GCN Layer

        ↓

Binary Classification Output

```

---

# GCN Mathematical Formulation

The implemented message passing operation follows:

$$
H^{(l+1)}
$$

$$
= \sigma
\left(
\hat{D}^{-\frac{1}{2}}
\hat{A}
\hat{D}^{-\frac{1}{2}}
H^{(l)}
W^{(l)}
\right)
$$


where:

* $\hat{A}$ is adjacency matrix with self-loops
* $\hat{D}$ is degree matrix
* $H^{(l)}$ represents node embeddings
* $W^{(l)}$ represents trainable weights
* $\sigma$ represents activation function

---

# Training Configuration

| Parameter         |                Value |
| ----------------- | -------------------: |
| Model             |                  GCN |
| Layers            |                    2 |
| Hidden dimension  |                   64 |
| Dropout           |                 0.35 |
| Optimizer         |                 Adam |
| Learning rate     |                 0.01 |
| Weight decay      |               0.0005 |
| Epochs            |                  250 |
| Gradient clipping |                  1.0 |
| Loss              | Binary Cross Entropy |
| Early stopping    |            30 epochs |
| Monitoring metric |        Validation F1 |

Environment:

```
Python 3.12
PyTorch 2.9.0
Device: CPU
CUDA: unavailable
```

---

# Experimental Setup

Dataset split:

| Split      | Nodes |
| ---------- | ----: |
| Training   |   178 |
| Validation |    44 |

Configuration:

```
Validation ratio: 0.2
Random seed: 42
```

No overlap exists between training and validation nodes.

---

# Results

## Best Validation Performance

Best checkpoint:

```
Epoch 37
```

| Metric            | Score |
| ----------------- | ----: |
| Accuracy          | 0.795 |
| Precision         | 0.778 |
| Recall            | 0.737 |
| F1-score          | 0.757 |
| Balanced Accuracy | 0.788 |
| AUROC             | 0.741 |
| AUPRC             | 0.795 |
| MCC               | 0.581 |

---

# Confusion Matrix

|          | Predicted 0 | Predicted 1 |
| -------- | ----------: | ----------: |
| Actual 0 |          21 |           4 |
| Actual 1 |           5 |          14 |

---

# Interpretation

The experiments demonstrate that:

* Graph topology contains predictive biological information.
* Pathway membership features improve enzyme classification.
* GCN message passing captures local pathway relationships.
* Regularization methods reduce overfitting on small biological graphs.

---

# Limitations

## Sparse Network Structure

The graph contains disconnected components and isolated enzymes, limiting neighborhood information propagation.

## Limited Biological Features

The current model uses only graph-derived and pathway-based features.

Future improvements may integrate:

* Protein sequence embeddings
* Gene expression profiles
* Protein-protein interaction networks
* Functional annotations

## Dataset Scale

The current graph contains 222 enzyme nodes, limiting model complexity.

---

# Future Directions

Possible research extensions:

### Advanced Graph Neural Networks

* Graph Attention Networks (GAT)
* GraphSAGE
* Graph Transformer architectures

### Multi-Modal Biological Learning

Integration of:

* Protein language models
* Transcriptomics
* Structural biology information

### Large-Scale Applications

Potential applications:

* Human metabolic networks
* Disease pathway analysis
* Comparative pathway modeling

---

# Repository Structure

```
gnn-metabolic-pathway-analysis/

│
├── .gitignore
├── LICENSE
├── README.md
│
├── notebooks/
│   └── GNN for Metabolic Pathway Analysis.ipynb
│
├── reports/
│   └── project Report.pdf
│
└── results/
    └── results.json

```

---

# Reproducibility

Clone repository:

```bash
git clone https://github.com/hannahfathi99/graph-neural-network-metabolic-pathway-analysis.git
```

Run notebook:

```bash
jupyter notebook "notebooks/GNN for Metabolic Pathway Analysis.ipynb"
```

Experimental outputs are available in:

```
results/results.json
```

including:

* Model configuration
* Graph statistics
* Training history
* Evaluation metrics

---

# Data Availability

KEGG pathway information is publicly available.

This repository contains:

* Computational implementation
* Experimental results
* Research documentation

Raw KEGG files are not redistributed.

---

# Citation

```
Fathi, Hannah.

Graph Neural Networks for Metabolic Pathway Analysis:
A From-Scratch Graph Convolutional Network Framework for Enzyme Reaction Participation Prediction.

Bioinformatics Research Project.
Fall 2025.
```

---

# Author

**Hannah Fathi**

M.Sc. Artificial Intelligence Student

Research Interests:

- Bioinformatics
- Graph Neural Networks
- Deep Learning
- Computer Vision
- Machine Learning

---

# License

MIT License
