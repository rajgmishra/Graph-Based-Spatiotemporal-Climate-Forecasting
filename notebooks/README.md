# Research Notebooks

This directory contains the Jupyter notebooks supporting the study:

**Graph-Based Spatiotemporal Modeling for Rainfall and Temperature Forecasting**

The notebooks implement baseline forecasting models, graph-based spatial representation learning, annual forecasting, evaluation, sensitivity analysis, end-to-end fine-tuning, an adapted HiSTGNN benchmark, and visualization.

The repository also contains a separate **Data Preparation** folder and a folder with the prepared annual datasets and verified spatial graph.

## Repository Contents

| Folder / notebook | Purpose |
|---|---|
| [Data Preparation/](./Data%20Preparation/) | Validation and preparation of rainfall and maximum-temperature source data. See its [README](./Data%20Preparation/README_Data_Preparation.md). |
| [Spatiotemporal_GNN_Kaggle_Data/](./Spatiotemporal_GNN_Kaggle_Data/) | Model-ready annual datasets, metadata, and verified static spatial graph used by the forecasting notebooks. |
| [01-gru-baseline-hyperparameter-tuning-ipynb.ipynb](./01-gru-baseline-hyperparameter-tuning-ipynb.ipynb) | GRU baseline hyperparameter search, validation-based configuration selection, and final test evaluation. |
| [02-lstm-baseline-hyperparameter-tuning-ipynb.ipynb](./02-lstm-baseline-hyperparameter-tuning-ipynb.ipynb) | LSTM baseline training, hyperparameter evaluation, validation-based selection, and held-out test evaluation. |
| [03-spatial-encoder-comparison-gcn-gat-gatv2-ipynb.ipynb](./03-spatial-encoder-comparison-gcn-gat-gatv2-ipynb.ipynb) | Comparison of GCN, GAT, and GATv2 spatial encoders using the verified graph and reconstruction objective. |
| [04-gatv2-spatial-embedding-extraction-ipynb.ipynb](./04-gatv2-spatial-embedding-extraction-ipynb.ipynb) | Loads trained GATv2 checkpoints and extracts yearly node embeddings for the three evaluated seeds. |
| [05-gatv2-gru-forecasting-ipynb.ipynb](./05-gatv2-gru-forecasting-ipynb.ipynb) | Builds and evaluates the GATv2-GRU forecasting model using the annual datasets and extracted spatial embeddings. |
| [06-rainfall-forecasting-evaluation-metrics-ipynb.ipynb](./06-rainfall-forecasting-evaluation-metrics-ipynb.ipynb) | Computes additional rainfall forecasting evaluation results, including threshold-based categorical measures (CSI and HSS). |
| [07-annual-ablation-robustness-experiments-ipynb.ipynb](./07-annual-ablation-robustness-experiments-ipynb.ipynb) | Conducts a GATv2 spatial-graph sensitivity experiment by evaluating alternative k-NN neighborhood sizes. |
| [08-end-to-end-gatv2-gru-experiment-ipynb.ipynb](./08-end-to-end-gatv2-gru-experiment-ipynb.ipynb) | Compares the frozen two-stage GATv2-GRU model with an end-to-end fine-tuned model, including independent test evaluation. |
| [09-final-forecasting-visualization-ipynb.ipynb](./09-final-forecasting-visualization-ipynb.ipynb) | Produces final forecasting visualizations from the model predictions and evaluation outputs. |
| [histgnn-ipynb.ipynb](./histgnn-ipynb.ipynb) | Implements a HiSTGNN-adapted spatiotemporal forecasting model and compares its 2024–2025 test performance with a persistence baseline. |

## Dataset and Forecasting Setup

The prepared data cover India for **2000–2025**, with 5,040 spatial nodes. Rainfall and maximum temperature are represented on a common 0.25° × 0.25° grid. The graph is a directed, unweighted Haversine-distance k-nearest-neighbor graph with k = 8 and 40,320 directed edges.

The forecasting task uses a 10-year input window to predict the following year. The chronological target-year splits are:

| Split | Target years | Number of annual samples |
|---|---:|---:|
| Training | 2010–2017 | 8 |
| Validation | 2018–2023 | 6 |
| Testing | 2024–2025 | 2 |

The model-ready input contains four features: normalized rainfall, normalized maximum temperature, and the corresponding rainfall and temperature missingness indicators. The target variables are rainfall (mm/day) and maximum temperature (°C). Target observation masks are provided so missing target values are excluded from loss and evaluation.

Further preprocessing details, including the annual coverage threshold, training-only imputation and normalization, and the NPZ file schema, are documented in [Data Preparation/README](./Data%20Preparation/README_Data_Preparation.md) and in `annual_forecasting_metadata.json`.

## Recommended Execution Workflow

The notebooks are organized by experimental stage. The following order describes the main dependency flow; the raw-data preparation stage is only needed when recreating the processed annual datasets from the original IMD data.

### Stage A — Prepare or obtain the model-ready data

1. Run the four notebooks in [Data Preparation/](./Data%20Preparation/) in their documented order if starting from the original rainfall and temperature data.
2. Alternatively, use the prepared files in [Spatiotemporal_GNN_Kaggle_Data/](./Spatiotemporal_GNN_Kaggle_Data/).

### Stage B — Baseline forecasting

3. Run Notebook 01 for GRU baseline tuning and evaluation.
4. Run Notebook 02 for LSTM baseline tuning and evaluation.

These baseline experiments use the annual model-ready data and can be run independently of the graph encoder workflow.

### Stage C — Graph-based spatial representation and forecasting

5. Run Notebook 03 to compare GCN, GAT, and GATv2 and save the selected spatial encoder checkpoints.
6. Run Notebook 04 to extract GATv2 embeddings from the trained checkpoints.
7. Run Notebook 05 to train and evaluate the GATv2-GRU forecasting model using the annual data and extracted embeddings.

### Stage D — Additional evaluation and experiments

8. Run Notebook 06 for the additional rainfall evaluation metrics. It requires the appropriate model predictions and test data.
9. Run Notebook 07 for the k-NN graph sensitivity experiment.
10. Run Notebook 08 for the frozen-versus-end-to-end comparison. This notebook uses pretrained spatial and temporal checkpoints in addition to the model-ready data.
11. Run Notebook 09 to generate the final forecasting visualizations from the required prediction/evaluation outputs.

Notebooks 06–09 are additional evaluation or analysis stages and may depend on outputs generated by earlier notebooks. Check each notebook's path/configuration cells before execution.

### Stage E — HiSTGNN-adapted benchmark

12. Run [histgnn-ipynb.ipynb](./histgnn-ipynb.ipynb) as a separate benchmark experiment. It uses the prepared annual training, validation, and test files, the forecasting metadata, and the verified spatial graph. It does not require the GATv2 embeddings or checkpoints generated by Notebooks 03–05.

The HiSTGNN-adapted notebook defines local variable-graph learning for rainfall and maximum temperature, sparse adaptive spatial graph convolution over candidate geographical edges, gated dilated inception temporal convolutions, and dynamic interaction between local and global representations. It trains with masked target loss, selects checkpoints using validation performance, evaluates the 2024–2025 test years, and includes a persistence-baseline comparison.

This notebook is an adapted implementation for the present dataset and experimental setup. It is not presented as an exact reproduction of the original HiSTGNN architecture or its reported experiments.

## Expected Model-Ready Files

The folder [Spatiotemporal_GNN_Kaggle_Data/](./Spatiotemporal_GNN_Kaggle_Data/) contains the following files:

| File | Description |
|---|---|
| `training_annual.npz` | Annual training input sequences, targets, masks, indices, and target years. |
| `validation_annual.npz` | Annual validation input sequences, targets, masks, indices, and target years. |
| `testing_annual.npz` | Annual test input sequences, targets, masks, indices, and target years. |
| `annual_forecasting_metadata.json` | Forecasting configuration, splits, feature definitions, preprocessing statistics, and graph metadata. |
| `static_spatial_graph_verified.pt` | Verified PyTorch Geometric graph object containing the static graph connectivity. |

The NPZ files contain the arrays `X`, `Y`, `Y_mask`, `input_indices`, `target_indices`, and `target_years`.

## HiSTGNN-Adapted Experiment Details

The adapted model uses the same 10-year input sequences, 5,040 spatial nodes, four input features, and two target variables as the main forecasting experiments. The verified geographical graph provides the candidate edges for sparse adaptive spatial learning. The model also learns an adaptive relationship between the two meteorological variables.

The notebook's main configuration uses a hidden dimension of 32, graph embedding dimension of 16, two model layers, dropout of 0.10, and a batch size of 1. Training uses AdamW with a learning rate of 0.001 and weight decay of 0.00001. The initial training configuration allows up to 200 epochs with early stopping based on validation loss. An extended-training stage is also included; the final checkpoint is selected according to validation performance.

The notebook reports masked test metrics separately for rainfall and maximum temperature, both per year and combined over 2024–2025. It also evaluates a persistence baseline. The executed comparison table in the notebook reports the following combined test results:

| Variable | Model | MAE | RMSE | Bias | R² |
|---|---|---:|---:|---:|---:|
| Rainfall | HiSTGNN-adapted | 0.6141 | 0.8738 | -0.1317 | 0.8307 |
| Rainfall | Persistence | 0.6398 | 0.8934 | -0.2649 | 0.8230 |
| Maximum temperature | HiSTGNN-adapted | 0.4653 | 0.6687 | 0.0311 | 0.9406 |
| Maximum temperature | Persistence | 0.4589 | 0.6280 | 0.1295 | 0.9476 |

These are the results stored in the notebook's comparison output. They are from the adapted implementation and should be interpreted within this study's two-year test period and single-seed experiment, rather than as a direct comparison with results reported in the original HiSTGNN publication.

The notebook saves the best initial checkpoint as `/kaggle/working/histgnn_adapted_best.pt` and the extended-training checkpoint as `/kaggle/working/histgnn_adapted_extended_best.pt`. It then selects the checkpoint with the better validation loss.

## Outputs and Dependencies

The notebooks save checkpoints, embeddings, result tables, and figures under their configured output directories, commonly under `/kaggle/working/`. Some notebooks also load checkpoints or embeddings produced by earlier notebooks or provided through Kaggle notebook/dataset inputs.

In particular:

- Notebook 04 requires the trained spatial-encoder checkpoints from Notebook 03.
- Notebook 05 requires the model-ready annual datasets and GATv2 embeddings from Notebook 04.
- Notebook 06 requires the relevant test predictions and associated data/model outputs.
- Notebook 08 requires the pretrained spatial and temporal checkpoints for the three-seed comparison.
- Notebook 09 requires the prediction and evaluation artifacts referenced in its configuration cells.
- The HiSTGNN-adapted notebook requires the five model-ready files listed above but is independent of the GATv2 checkpoint and embedding workflow. It also installs/imports PyTorch Geometric to load and process the graph object.

For reproducibility, retain the generated checkpoints and outputs or make them available as Kaggle dataset/notebook inputs when running dependent notebooks in a fresh session.

## Software and Execution Environment

The notebooks were developed and executed in a Kaggle notebook environment. They use Python and common scientific-computing and deep-learning libraries, including NumPy, Pandas, Matplotlib, scikit-learn, PyTorch, and PyTorch Geometric. Data-preparation notebooks additionally use packages for NetCDF and geospatial data handling.

The HiSTGNN-adapted notebook uses PyTorch and PyTorch Geometric and includes a package installation cell for `torch_geometric`. Exact package versions may depend on the Kaggle runtime. Where a notebook contains an installation or environment-setup cell, review it before execution. GPU acceleration is recommended for the graph-learning and neural forecasting experiments.

## Important Reproducibility Notes

- Update the input and output paths if running outside the original Kaggle environment. Several notebooks currently use Kaggle-specific paths.
- Ensure the prepared data, graph, checkpoints, and embeddings are available at the locations specified in each notebook.
- Use the same chronological training, validation, and testing splits documented above.
- Preserve the training-only preprocessing and normalization procedure; do not derive preprocessing statistics from validation or test data.
- Use the validation set for model selection and reserve 2024–2025 for final test evaluation.
- The notebooks document different experimental stages. A fresh end-to-end run may require executing notebooks in the dependency order and retaining intermediate artifacts.
- The HiSTGNN-adapted experiment is a separate implementation using this repository's graph and annual data. Do not interpret it as an exact reproduction of the original HiSTGNN model.

## Data Sources

The rainfall and maximum-temperature source datasets are provided by the India Meteorological Department (IMD). Refer to the Data Preparation documentation and the manuscript references for the source dataset descriptions and citations.

For details of the complete study, methodology, experimental design, and reported results, please refer to the associated manuscript: **Graph-Based Spatiotemporal Modeling for Rainfall and Temperature Forecasting**.
