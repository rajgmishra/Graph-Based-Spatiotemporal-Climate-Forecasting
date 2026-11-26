# Graph-Based Spatiotemporal Modeling for Rainfall and Temperature Forecasting

This repository contains the data-preparation workflow, model implementations, experiment notebooks, and selected result files for the study **Graph-Based Spatiotemporal Modeling for Rainfall and Temperature Forecasting**.

The project investigates annual one-year-ahead forecasting of rainfall and maximum temperature over India using recurrent neural networks and graph-based spatiotemporal models.

## Study Overview

The study uses daily climate observations from the India Meteorological Department (IMD) for **2000–2025**. The climate variables are aligned to a common set of **5,040 spatial research nodes**.

The forecasting setup is summarized below.

| Item | Description |
|---|---|
| Study region | India |
| Historical data period | 2000–2025 |
| Climate variables | Rainfall and daily maximum temperature |
| Rainfall source-grid resolution | 0.25° × 0.25° |
| Temperature source-grid resolution | 1° × 1°; interpolated to the research nodes |
| Number of spatial nodes | 5,040 |
| Forecasting task | Annual one-year-ahead forecasting |
| Input window | 10 preceding years |
| Target variables | Rainfall (mm/day) and maximum temperature (°C) |

The annual target-year splits are chronological:

| Dataset split | Target years | Annual samples |
|---|---:|---:|
| Training | 2010–2017 | 8 |
| Validation | 2018–2023 | 6 |
| Testing | 2024–2025 | 2 |

The model-ready input contains four features: normalized rainfall, normalized maximum temperature, and the corresponding rainfall and temperature missingness indicators. Target observations are accompanied by validity masks; missing target values are not imputed.

## Repository Structure

```text
.
├── data/
│   ├── rainfall/
│   │   └── README.md
│   └── temperature/
│       └── README.md
│
├── notebooks/
│   ├── Data Preparation/
│   │   ├── 1_rainfall_data_validation.ipynb
│   │   ├── 2_temperature_data_validation.ipynb
│   │   ├── 3_integrated_climate_analysis.ipynb
│   │   ├── 4_model_ready_data_preparation.ipynb
│   │   └── README_Data_Preparation.md
│   │
│   ├── Spatiotemporal_GNN_Kaggle_Data/
│   │   ├── training_annual.npz
│   │   ├── validation_annual.npz
│   │   ├── testing_annual.npz
│   │   ├── annual_forecasting_metadata.json
│   │   ├── static_spatial_graph_verified.pt
│   │   └── Readme.md
│   │
│   ├── 01-gru-baseline-hyperparameter-tuning-ipynb.ipynb
│   ├── 02-lstm-baseline-hyperparameter-tuning-ipynb.ipynb
│   ├── 03-spatial-encoder-comparison-gcn-gat-gatv2-ipynb.ipynb
│   ├── 04-gatv2-spatial-embedding-extraction-ipynb.ipynb
│   ├── 05-gatv2-gru-forecasting-ipynb.ipynb
│   ├── 06-rainfall-forecasting-evaluation-metrics-ipynb.ipynb
│   ├── 07-annual-ablation-robustness-experiments-ipynb.ipynb
│   ├── 08-end-to-end-gatv2-gru-experiment-ipynb.ipynb
│   ├── 09-final-forecasting-visualization-ipynb.ipynb
│   ├── histgnn-ipynb.ipynb
│   └── README.md
│
├── output/
│   └── Readme.md
│   (selected CSV and TXT experiment summaries)
│
├── requirements.txt
├── LICENSE
└── README.md
```

The detailed notebook inventory and dependencies are described in [notebooks/README.md](notebooks/README.md). The result files are described in [output/Readme.md](output/Readme.md).

## Data Sources

The original daily climate datasets are obtained from the India Meteorological Department (IMD).

- **Rainfall:** [IMD 0.25° gridded rainfall data](https://www.imdpune.gov.in/cmpg/Griddata/Rainfall_25_NetCDF.html). The study uses 26 annual NetCDF files covering 2000–2025.
- **Maximum temperature:** [IMD gridded maximum-temperature data](https://www.imdpune.gov.in/cmpg/Griddata/Max_1_Bin.html). The source inventory consists of 24 NetCDF files for 2000–2023 and two GRD files for 2024–2025.

The raw IMD files are not included in this repository. Download them from the respective IMD portals and place them in the required input directories. See the data-specific documentation for expected filenames, formats, and preprocessing details:

- [Rainfall data documentation](data/rainfall/README.md)
- [Maximum-temperature data documentation](data/temperature/README.md)

**Input-path note:** the data-preparation notebooks currently configure their raw inputs under `data/raw/rainfall/` and `data/raw/temperature/`, while the documentation folders in this repository are `data/rainfall/` and `data/temperature/`. Before running the raw-data notebooks, either arrange the downloaded files in the configured `data/raw/` locations or edit the `RAINFALL_DIR` and `TEMPERATURE_DIR` path variables in the notebooks.

## Model-Ready Dataset and Spatial Graph

Prepared annual datasets are provided in [notebooks/Spatiotemporal_GNN_Kaggle_Data/](notebooks/Spatiotemporal_GNN_Kaggle_Data/). This folder contains:

| File | Purpose |
|---|---|
| `training_annual.npz` | Training sequences and targets for target years 2010–2017 |
| `validation_annual.npz` | Validation sequences and targets for target years 2018–2023 |
| `testing_annual.npz` | Test sequences and targets for target years 2024–2025 |
| `annual_forecasting_metadata.json` | Forecast configuration, split definitions, feature descriptions, preprocessing statistics, and graph metadata |
| `static_spatial_graph_verified.pt` | Verified PyTorch Geometric graph object |

Each NPZ file includes `X`, `Y`, `Y_mask`, `input_indices`, `target_indices`, and `target_years`. The input tensor has four features per node and time step. Target values are stored in physical units with a separate validity mask.

The spatial graph is a **directed, unweighted Haversine-distance k-nearest-neighbor graph** with (k=8), 5,040 nodes, and 40,320 directed edges. It has no self-loops. It represents fixed geographical neighbourhoods; it is not a learned atmospheric interaction or teleconnection graph.

The model-ready preparation uses a 90% annual observation-coverage criterion. Input imputation and normalization are based on training data only, with node-wise training means and a global training-mean fallback. Target values are not imputed.

For array dimensions, metadata fields, loading examples, and graph details, see the [model-ready data README](notebooks/Spatiotemporal_GNN_Kaggle_Data/Readme.md).

## Models and Experiments

The notebooks cover the following experiments:

1. **Recurrent baselines:** GRU and LSTM hyperparameter tuning, validation-based model selection, and held-out test evaluation.
2. **Spatial encoder comparison:** GCN, GAT, and GATv2 trained and compared using the spatial reconstruction objective.
3. **Graph-based forecasting:** extraction of GATv2 spatial embeddings followed by GATv2-GRU forecasting.
4. **Additional rainfall evaluation:** rainfall threshold-based Critical Success Index (CSI) and Heidke Skill Score (HSS) at 2.5 and 5.0 mm/day.
5. **Graph sensitivity:** evaluation of alternative k-nearest-neighbour sizes ((k=4, 8, 12)).
6. **End-to-end fine-tuning:** comparison of the frozen two-stage GATv2-GRU workflow with end-to-end fine-tuning.
7. **HiSTGNN-adapted benchmark:** an adapted spatiotemporal model with adaptive local and global graph components, temporal convolutions, and local–global interaction.

The HiSTGNN notebook is an **adapted implementation for this study's data and experimental setup**. It should not be interpreted as an exact reproduction of the original HiSTGNN architecture or its published experiments.

For the full notebook descriptions and recommended order, see [notebooks/README.md](notebooks/README.md).

## Installation

Use Python 3.10 or later as a practical starting point, and create a virtual environment.

### Windows

```bash
python -m venv climate_env
climate_env\\Scripts\\activate
```

### Linux / macOS

```bash
python -m venv climate_env
source climate_env/bin/activate
```

Install the project dependencies:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

The dependency list includes NumPy, Pandas, SciPy, scikit-learn, Matplotlib, Xarray, netCDF4, GeoPandas, PyTorch, PyTorch Geometric, and Jupyter components.

**PyTorch installation:** the appropriate PyTorch build depends on the available CPU/CUDA environment. For GPU use, install a compatible PyTorch/CUDA build for your system before installing or configuring PyTorch Geometric. Review the official installation instructions if a platform-specific wheel is required.

## Recommended Execution Workflow

### Option 1: Use the prepared model-ready data

1. Install the dependencies.
2. Make the files in [Spatiotemporal_GNN_Kaggle_Data/](notebooks/Spatiotemporal_GNN_Kaggle_Data/) available to the notebooks.
3. Run the baseline and graph-based experiment notebooks in the order described in [notebooks/README.md](notebooks/README.md).
4. Run the additional evaluation, fine-tuning, visualization, or HiSTGNN-adapted notebook as required.

This is the most direct route for users who want to work with the supplied annual datasets rather than rebuild them from the raw IMD files.

### Option 2: Rebuild the model-ready data from the raw IMD files

1. Download the rainfall and maximum-temperature data from the IMD portals.
2. Configure the input paths in the data-preparation notebooks.
3. Execute the four notebooks in [notebooks/Data Preparation/](notebooks/Data%20Preparation/) in this order:
   - `1_rainfall_data_validation.ipynb`
   - `2_temperature_data_validation.ipynb`
   - `3_integrated_climate_analysis.ipynb`
   - `4_model_ready_data_preparation.ipynb`
4. Use the generated annual datasets and spatial graph in the downstream modeling notebooks.

Some experiment notebooks depend on checkpoints, embeddings, or prediction files generated by earlier notebooks. Retain those intermediate files or update the configured paths when running in a new environment. Many notebook cells were developed in Kaggle and use Kaggle-specific paths such as `/kaggle/input/` and `/kaggle/working/`; these paths must be adapted for a local environment.

## Experimental Outputs

The [output/](output/) folder contains selected CSV and TXT summaries, including:

- GRU and LSTM validation and test metrics.
- GCN, GAT, and GATv2 reconstruction results.
- k-NN graph sensitivity results.
- GATv2-GRU tuning summaries.
- Rainfall CSI and HSS results.
- Frozen-versus-end-to-end experiment summaries and test metrics.
- Figure-generation metric checks.

These are selected exported summaries, not a complete archive of model checkpoints or every intermediate artifact. Some files report results from distinct experiment configurations; consult [output/Readme.md](output/Readme.md) before comparing values across files.

## Reproducibility Notes

- Preserve the chronological training, validation, and testing splits.
- Use the validation data for model selection and early stopping; reserve 2024–2025 for final test evaluation.
- Fit imputation and normalization procedures on training data only.
- Retain target observation masks and do not impute missing target values.
- Check notebook path and configuration cells before execution. Outputs and intermediate checkpoints are not all stored in the repository.
- The original IMD raw data must be downloaded separately.

The notebooks and selected model-ready data are provided to support inspection and reproduction of the documented experiments. Exact execution may require adapting file paths and installing a PyTorch build compatible with the user's hardware.

## Citation

If you use this work, please cite the associated study:

Patel, P. J., Mishra, R. G., Kumar, A., and Pant, S.  
*Graph-Based Spatiotemporal Modeling for Rainfall and Temperature Forecasting.*

Please use the final journal or conference citation, including publication year and DOI, when that information becomes available.

## License

This project is distributed under the [MIT License](LICENSE).
