# Graph-Based Spatiotemporal Modeling for Rainfall and Temperature Forecasting

## Overview

This repository contains the implementation used in the manuscript:

**Graph-Based Spatiotemporal Modeling for Rainfall and Temperature Forecasting**

The proposed framework combines:

* Haversine distance based k-nearest neighbor (k-NN) graph construction
* Graph Convolution Network (GCN), Graph Attention Network (GAT), and Graph Attention Network v2 (GATv2) for spatial representation learning
* LSTM and GRU networks for temporal forecasting

The study performs year-ahead forecasting of aggregated yearly rainfall and maximum temperature over India using graph based spatiotemporal learning.

---

## Repository Structure

```text
data/
├── rainfall/
│   └── README.md
└── temperature/
    └── README.md

notebooks/
├── 1_build_static_graph.ipynb
├── 2-spatial-gatv2-training-revised.ipynb
├── 3-temporal-forecasting-lstm-gru-revised.ipynb
└── README.md

LICENSE
README.md
requirements.txt
```

---

## Dataset

The climate datasets used in this study are obtained from the India Meteorological Department (IMD).

### Rainfall Dataset

https://www.imdpune.gov.in/cmpg/Griddata/Rainfall_25_NetCDF.html

### Maximum Temperature Dataset

https://www.imdpune.gov.in/cmpg/Griddata/Max_1_Bin.html

Download the required datasets and place them in the corresponding folders:

```text
data/rainfall/
data/temperature/
```

Additional details regarding the expected files and directory structure are provided in the README files within each data folder.

---

## Installation

Create a Python environment:

```bash
python -m venv climate_env
```

Activate the environment:

### Windows

```bash
climate_env\Scripts\activate
```

### Linux / macOS

```bash
source climate_env/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

---

## Execution Order

Run the notebooks in the following sequence.

### Step 1: Graph Construction

```text
1_build_static_graph.ipynb
```

This notebook:

* Loads rainfall and temperature datasets
* Performs preprocessing
* Constructs the static k-NN graph
* Computes Haversine distance based neighborhood relationships

---

### Step 2: Spatial Representation Learning

```text
2-spatial-gatv2-training-revised.ipynb
```

This notebook:

* Trains GCN
* Trains GAT
* Trains GATv2
* Compares spatial reconstruction performance
* Generates spatial embeddings

Corresponds to the spatial encoder evaluation reported in the manuscript.

---

### Step 3: Temporal Forecasting

```text
3-temporal-forecasting-lstm-gru-revised.ipynb
```

This notebook:

* Performs hyperparameter tuning
* Trains LSTM models
* Trains GRU models
* Trains GATv2-LSTM models
* Trains GATv2-GRU models
* Evaluates forecasting performance

Corresponds to the forecasting experiments reported in the manuscript.

---

## Expected Results

### Spatial Encoder Evaluation

| Model | Reconstruction MSE |
| ----- | ------------------ |
| GCN   | ~0.366             |
| GAT   | ~0.184             |
| GATv2 | ~0.138             |

### Forecasting Performance

| Model      | R²     |
| ---------- | ------ |
| LSTM       | ~0.851 |
| GRU        | ~0.964 |
| GATv2-LSTM | ~0.872 |
| GATv2-GRU  | ~0.971 |

Minor numerical variations may occur due to differences in software versions, hardware configuration, and random initialization.

---

## Reproducibility

All experiments reported in the manuscript can be reproduced using the notebooks provided in this repository together with the corresponding IMD rainfall and temperature datasets.

---

## Citation

If you use this repository, please cite:

Patel, P. J., Mishra, R. G., Kumar, A., and Pant, S.

*Graph-Based Spatiotemporal Modeling for Rainfall and Temperature Forecasting.*

---

## License

This project is distributed under the MIT License.
