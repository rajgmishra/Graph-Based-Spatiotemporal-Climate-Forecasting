# Model-Ready Annual Forecasting Data

This folder contains the processed data files and verified spatial graph used by the notebooks in this repository for annual rainfall and maximum-temperature forecasting over India.

The files are prepared for a **one-year-ahead forecasting task** using a 10-year historical input window and 5,040 spatial nodes. The training, validation, and testing datasets are stored separately to preserve the chronological evaluation design.

## Folder Contents

| File | Format | Description |
|---|---|---|
| `training_annual.npz` | NumPy compressed archive | Model-ready input sequences and forecasting targets for target years 2010–2017. |
| `validation_annual.npz` | NumPy compressed archive | Model-ready input sequences and forecasting targets for target years 2018–2023. |
| `testing_annual.npz` | NumPy compressed archive | Model-ready input sequences and forecasting targets for target years 2024–2025. |
| `annual_forecasting_metadata.json` | JSON | Describes the forecasting task, data splits, spatial nodes, input features, target variables, preprocessing settings, normalization statistics, and graph configuration. |
| `static_spatial_graph_verified.pt` | PyTorch serialized object | Verified PyTorch Geometric graph object containing the static Haversine k-nearest-neighbor connectivity used by the graph-based models. |

## 1. Forecasting Dataset Overview

The prepared data cover **2000–2025** and contain two target variables:

- **Rainfall:** annual average of daily rainfall observations, expressed in mm/day.
- **Maximum temperature:** annual average of daily maximum-temperature observations, expressed in °C.

Both variables are represented on a common 0.25° × 0.25° spatial grid, with **5,040 spatial nodes**.

The forecasting task uses the preceding **10 years** of observations to predict the following year. The chronological target-year splits are:

| Split | Target years | Number of samples |
|---|---|---:|
| Training | 2010–2017 | 8 |
| Validation | 2018–2023 | 6 |
| Testing | 2024–2025 | 2 |

The validation split is used for model selection and early stopping. The 2024–2025 test split is reserved for final evaluation.

## 2. Annual NPZ Files

The files `training_annual.npz`, `validation_annual.npz`, and `testing_annual.npz` have the same internal structure. Each is a compressed NumPy archive containing the following arrays:

| Array | Data type | Description |
|---|---|---|
| `X` | `float32` | Input sequences with normalized rainfall, normalized maximum temperature, and the two missingness indicators. |
| `Y` | `float32` | Target rainfall and maximum-temperature values for the forecast year. Missing target observations are represented by NaN values. |
| `Y_mask` | `bool` | Target-observation mask. `True` identifies a valid observed target; `False` identifies a missing target. |
| `input_indices` | `int64` | Historical input-year indices for each forecasting sample. |
| `target_indices` | `int64` | Index of the target year in the historical time sequence. |
| `target_years` | `int64` | Calendar year being forecast for each sample. |

### Array dimensions

| Array | Training | Validation | Testing |
|---|---|---|---|
| `X` | (8, 10, 5040, 4) | (6, 10, 5040, 4) | (2, 10, 5040, 4) |
| `Y` | (8, 5040, 2) | (6, 5040, 2) | (2, 5040, 2) |
| `Y_mask` | (8, 5040, 2) | (6, 5040, 2) | (2, 5040, 2) |
| `input_indices` | (8, 10) | (6, 10) | (2, 10) |
| `target_indices` | (8,) | (6,) | (2,) |
| `target_years` | (8,) | (6,) | (2,) |

The four input features in `X` are ordered as follows:

1. Normalized rainfall
2. Normalized maximum temperature
3. Rainfall missingness indicator
4. Temperature missingness indicator

The final dimension of `Y` contains two target variables in this order:

1. Rainfall (mm/day)
2. Maximum temperature (°C)

The target values are retained with their observation masks; target values are not imputed. During training and evaluation, the masks are used to exclude unavailable target observations from loss and metric calculations.

## 3. Preprocessing and Metadata

The `annual_forecasting_metadata.json` file records the configuration used to create the model-ready annual datasets. Its principal fields include:

| Metadata field | Recorded setting |
|---|---|
| Forecasting task | Annual one-year-ahead forecasting |
| Input window | 10 years |
| Historical period | 2000–2025 |
| Number of spatial nodes | 5,040 |
| Number of input features | 4 |
| Target variables | Rainfall and maximum temperature |
| Annual coverage threshold | 90% |
| Imputation method | Training-only node-wise mean, with global training mean fallback |
| Target imputation | Disabled |
| Graph construction | Haversine k-nearest neighbours |
| Number of neighbours (`k`) | 8 |
| Number of directed graph edges | 40,320 |

The metadata also records the training-derived normalization statistics:

| Variable | Mean | Standard deviation |
|---|---:|---:|
| Rainfall | 3.0487177 | 2.0884185 |
| Maximum temperature | 31.2358303 | 2.8963938 |

These statistics are provided for reference and to support consistent preprocessing. The preprocessing workflow derives imputation and normalization information from the training period; validation and test observations are not used to estimate these statistics.

## 4. Verified Static Spatial Graph

The file `static_spatial_graph_verified.pt` contains a PyTorch Geometric `Data` object representing the spatial graph.

| Graph property | Value |
|---|---:|
| Number of nodes | 5,040 |
| Number of directed edges | 40,320 |
| Graph construction | Haversine-distance k-nearest neighbours |
| Number of neighbours (`k`) | 8 |
| Edge weights | Not stored; graph is unweighted |
| Self-loops | None |

The graph uses the fixed geographic locations of the 5,040 grid cells. Its connectivity is shared across the annual observations, while the node features change over time. The graph is used by the GCN, GAT, and GATv2 spatial encoder experiments and the subsequent GATv2-GRU forecasting workflow.

## 5. Loading the Files

The following examples illustrate how to load the prepared data.

### Load an annual dataset

```python
import numpy as np

data_dir = "path/to/Spatiotemporal_GNN_Kaggle_Data"

train = np.load(f"{data_dir}/training_annual.npz", allow_pickle=False)
validation = np.load(f"{data_dir}/validation_annual.npz", allow_pickle=False)
test = np.load(f"{data_dir}/testing_annual.npz", allow_pickle=False)

X_train = train["X"]
Y_train = train["Y"]
Y_train_mask = train["Y_mask"]

print("Training input:", X_train.shape)
print("Training target:", Y_train.shape)
print("Training target mask:", Y_train_mask.shape)

print("Training target years:", train["target_years"])
print("Validation target years:", validation["target_years"])
print("Testing target years:", test["target_years"])
```

### Load metadata

```python
import json

with open(f"{data_dir}/annual_forecasting_metadata.json", "r") as file:
    metadata = json.load(file)

print(metadata["forecasting_task"])
print(metadata["input_features"])
print(metadata["target_variables"])
print(metadata["graph"])
```

### Load the spatial graph

```python
import torch

graph = torch.load(
    f"{data_dir}/static_spatial_graph_verified.pt",
    map_location="cpu",
    weights_only=False
)

print(graph)
print("Nodes:", graph.num_nodes)
print("Edge index shape:", graph.edge_index.shape)
```

The graph file is a serialized PyTorch object. Load it only from this trusted research repository or another trusted source.

## 6. Use in the Research Notebooks

These files are the common data inputs for the downstream modeling notebooks:

- **GRU and LSTM baselines:** use the annual training, validation, and testing NPZ files.
- **Spatial encoder comparison:** uses the annual climate data and the verified spatial graph to compare GCN, GAT, and GATv2.
- **GATv2 embedding extraction:** uses the trained spatial encoder checkpoints, annual data, and spatial graph to generate node embeddings.
- **GATv2-GRU forecasting:** uses the annual forecasting splits and extracted GATv2 embeddings.
- **Rainfall evaluation:** uses the test targets, masks, and model predictions for additional rainfall metrics.
- **Ablation and robustness experiments:** use the annual datasets and graph information for additional model analysis.
- **End-to-end fine-tuning:** uses the annual data, graph, and pretrained model checkpoints.

See the parent [notebooks README](../README.md) for the full notebook list and recommended execution workflow.

## 7. Reproducibility Notes

- Keep the three NPZ split files together with the metadata and graph file.
- Preserve the chronological training, validation, and testing assignments.
- Use the provided `Y_mask` arrays when calculating training losses and evaluation metrics.
- Do not treat NaN target entries as observed values.
- Use training-derived preprocessing statistics consistently; do not recalculate them using validation or test data.
- The metadata contains the graph's preprocessing-path reference. In this repository, the graph file is stored alongside the annual NPZ files and metadata in this folder.
- The files in this folder are model-ready processed data and graph artifacts; they do not replace the original IMD source datasets or the raw-data preparation notebooks.

For the raw-data sources and details of rainfall and temperature validation and preprocessing, see [Data Preparation](../Data%20Preparation/README_Data_Preparation.md).
