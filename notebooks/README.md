# Notebooks

This folder contains the complete implementation used in the manuscript:

**Graph-Based Spatiotemporal Modeling for Rainfall and Temperature Forecasting**

The notebooks should be executed in the order listed below.

---

## 1. Graph Construction

### File

```text
1_build_static_graph.ipynb
```

### Purpose

This notebook performs data loading, preprocessing, and spatial graph construction.

### Main Tasks

* Load rainfall and maximum temperature datasets.
* Align spatial grids.
* Generate grid-cell coordinates.
* Construct a static k-nearest neighbor (k-NN) graph.
* Compute neighborhood relationships using Haversine distance.
* Create graph connectivity information for all spatial locations.

### Output

* Static graph structure.
* Edge index representation.
* Spatial graph used throughout the study.

---

## 2. Spatial Representation Learning

### File

```text
2_spatial_gatv2_training_revised.ipynb
```

### Purpose

This notebook evaluates graph neural network models for spatial representation learning.

### Main Tasks

* Train Graph Convolution Network (GCN).
* Train Graph Attention Network (GAT).
* Train Graph Attention Network Version 2 (GATv2).
* Compare spatial reconstruction performance.
* Generate spatial embeddings for all years.

### Output

* Spatial reconstruction errors.
* Comparison of GCN, GAT, and GATv2.
* Spatial embeddings used for temporal forecasting.
* Results corresponding to Table 1 and Figure 5 of the manuscript.

---

## 3. Temporal Forecasting

### File

```text
3_temporal_forecasting_lstm_gru_revised.ipynb
```

### Purpose

This notebook performs year-ahead forecasting using recurrent neural networks.

### Main Tasks

* Hyperparameter tuning.
* Train LSTM models.
* Train GRU models.
* Train GATv2-LSTM models.
* Train GATv2-GRU models.
* Evaluate forecasting performance using RMSE, MAE, and R².

### Output

* Ablation study results.
* Hyperparameter tuning results.
* Final forecasting performance.
* Observed versus predicted rainfall plots.
* Observed versus predicted temperature plots.
* Results corresponding to Tables 2–3 and Figures 6–7 of the manuscript.

---

## Execution Order

Run the notebooks sequentially:

```text
1_build_static_graph.ipynb
↓
2_spatial_gatv2_training_revised.ipynb
↓
3_temporal_forecasting_lstm_gru_revised.ipynb
```
