# Graph-Based Spatiotemporal Modeling for Rainfall and Temperature Forecasting

## Overview

This repository contains the implementation used in the manuscript:

**Graph-Based Spatiotemporal Modeling for Rainfall and Temperature Forecasting**

The framework combines:

* Haversine-distance-based k-nearest neighbor (k-NN) graph construction
* Graph Attention Network v2 (GATv2) for spatial representation learning
* LSTM and GRU networks for temporal forecasting

The study performs year-ahead forecasting of aggregated yearly rainfall and maximum temperature over India using graph-based spatiotemporal learning.

---

## Repository Structure

```text
data/
│
├── rainfall/
├── temperature/

notebooks/
│
├── 1_build_static_graph.ipynb
├── 2_spatial_gatv2_training_revised.ipynb
└── 3_temporal_forecasting_lstm_gru_revised.ipynb

outputs/
│
├── figures/
└── tables/
```

---

## Dataset

The climate datasets are obtained from the India Meteorological Department (IMD).

Rainfall Dataset:
https://www.imdpune.gov.in/cmpg/Griddata/Rainfall_25_NetCDF.html

Temperature Dataset:
https://www.imdpune.gov.in/cmpg/Griddata/Max_1_Bin.html

The processed datasets used in this study are provided in the `data/` directory.

---

## Installation

Create a Python environment:

```bash
python -m venv climate_env
source climate_env/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

---

## Execution Order

Run the notebooks in the following order.

### Step 1: Graph Construction

```text
1_build_static_graph.ipynb
```

This notebook:

* Loads gridded rainfall and temperature data
* Builds the static k-NN graph
* Uses Haversine distance
* Generates graph connectivity information

Output:

* Static graph structure

---

### Step 2: Spatial Representation Learning

```text
2_spatial_gatv2_training_revised.ipynb
```

This notebook:

* Trains GCN
* Trains GAT
* Trains GATv2
* Compares spatial reconstruction errors

Output:

* Table 1
* Figure 5
* Spatial embeddings

---

### Step 3: Temporal Forecasting

```text
3_temporal_forecasting_lstm_gru_revised.ipynb
```

This notebook:

* Performs hyperparameter tuning
* Trains LSTM
* Trains GRU
* Trains GATv2-LSTM
* Trains GATv2-GRU

Output:

* Table 2
* Table 3
* Figure 6
* Figure 7

---

## Expected Results

### Spatial Encoder Evaluation

| Model | Reconstruction MSE |
| ----- | ------------------ |
| GCN   | ~0.366             |
| GAT   | ~0.184             |
| GATv2 | ~0.138             |

---

### Final Forecasting Performance

| Model      | R²     |
| ---------- | ------ |
| LSTM       | ~0.851 |
| GRU        | ~0.964 |
| GATv2-LSTM | ~0.872 |
| GATv2-GRU  | ~0.971 |

Minor variations may occur because of hardware and software differences.

---

## Citation

If you use this repository, please cite:

Patel P.J., Mishra R.G., Kumar A., Pant S.

Graph-Based Spatiotemporal Modeling for Rainfall and Temperature Forecasting.

---

## License

MIT License.
