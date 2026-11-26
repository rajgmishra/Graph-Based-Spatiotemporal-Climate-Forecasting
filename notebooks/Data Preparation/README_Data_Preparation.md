# Data Preparation

This folder contains the notebooks used to validate, analyze, integrate, and prepare the rainfall and maximum-temperature datasets for the spatiotemporal forecasting experiments.

The data-preparation workflow uses daily climate observations over India and produces the processed datasets required by the downstream spatial-encoder and forecasting notebooks.

## Workflow

Execute the notebooks in the following order:

| Order | Notebook | Purpose |
|---|---|---|
| 1 | `1_rainfall_data_validation.ipynb` | Inspect and validate rainfall NetCDF data, temporal coverage, missing observations, spatial grid coverage, and the correspondence between rainfall grid cells and the research-node coordinates. |
| 2 | `2_temperature_data_validation.ipynb` | Validate maximum-temperature data, including NetCDF files for the historical period and GRD files for the most recent years; examine temporal/spatial coverage, grid consistency, and coordinate support. |
| 3 | `3_integrated_climate_analysis.ipynb` | Integrate rainfall and temperature data with the research-node coordinates and spatial graph; perform data-quality checks and prepare the integrated daily climate data and associated masks/metadata. |
| 4 | `4_model_ready_data_preparation.ipynb` | Perform leakage-aware imputation and normalization, aggregate daily observations to annual values, apply the annual coverage criterion, create annual forecasting samples, and save the final datasets for model training, validation, and testing. |

## Dataset Overview

The preparation pipeline covers the historical period **2000–2025** and uses two climate variables:

- Rainfall
- Maximum temperature

The study region is India. The spatial modeling framework uses **5,040 research nodes** and a verified spatial graph constructed using Haversine-distance-based k-nearest neighbors.

The annual forecasting task uses a **10-year input window** to predict climate values for the following year.

### Forecasting target-year splits

| Dataset split | Target years |
|---|---|
| Training | 2010–2017 |
| Validation | 2018–2023 |
| Testing | 2024–2025 |

The final model-ready annual dataset uses four input features:

1. Normalized rainfall
2. Normalized maximum temperature
3. Rainfall missingness indicator
4. Temperature missingness indicator

The target variables are rainfall (mm/day) and maximum temperature (°C). Target missing values are retained with observation masks rather than being imputed.

## Notebook Descriptions

### 1. Rainfall Data Validation

`1_rainfall_data_validation.ipynb`

This notebook examines the rainfall source data and its spatial/temporal coverage. The main checks include:

- Inspecting rainfall NetCDF files and dataset dimensions/metadata.
- Examining rainfall statistics and missing observations.
- Assessing valid rainfall observations across spatial grid cells.
- Comparing rainfall grid coverage with the research-node coordinates.
- Calculating nearest-grid distances and identifying nodes requiring spatial-coverage investigation.
- Generating coverage and mask-comparison reports.

The notebook is intended to establish the quality and spatial correspondence of the rainfall data before integration with the research-node dataset.

### 2. Temperature Data Validation

`2_temperature_data_validation.ipynb`

This notebook validates the maximum-temperature dataset and its spatial and temporal consistency.

The notebook includes:

- Inspection of historical temperature NetCDF files.
- Examination of the temperature data dimensions, coordinate information, time metadata, and statistics.
- Validation of the temperature GRD files for the later years.
- Inspection of GRD file structure and data dimensions.
- Comparison of spatial masks and coverage across years.
- Identification of common valid temperature-grid cells.
- Comparison of temperature-grid coordinates with the research-node coordinates.
- Assessment of spatial support, including interpolation-support checks.
- Verification of coordinate orientation and consistency across source files.

The notebook supports the assessment of temperature-data quality and spatial compatibility with the research-node framework.

### 3. Integrated Climate Analysis

`3_integrated_climate_analysis.ipynb`

This notebook integrates rainfall, maximum-temperature, research-node, and spatial-graph information.

The processing and validation steps include:

- Loading the rainfall and temperature datasets.
- Aligning climate data with the research-node coordinates.
- Checking spatial correspondence and node mappings.
- Verifying the spatial graph and its node indexing.
- Preparing integrated climate arrays and observation masks.
- Examining data coverage and missing observations.
- Preparing the data structures required for subsequent preprocessing and forecasting.

This notebook acts as the integration stage between source-data validation and final model-ready dataset preparation.

### 4. Model-Ready Data Preparation

`4_model_ready_data_preparation.ipynb`

This notebook prepares the final data used by the forecasting models.

The main processing stages include:

- Defining chronological training, validation, and testing periods.
- Calculating training-period climatological information.
- Performing leakage-aware missing-data handling.
- Using causal imputation procedures for the relevant data periods.
- Retaining masks that distinguish original observations from imputed values.
- Calculating normalization statistics using training data only.
- Normalizing rainfall and maximum-temperature input features.
- Preparing target values and target-observation masks.
- Aggregating daily climate observations into annual values.
- Calculating annual observation coverage.
- Applying the annual coverage threshold of **90%**.
- Constructing 10-year input sequences and one-year-ahead forecasting targets.
- Preparing the four input features, including missingness indicators.
- Saving the training, validation, and testing datasets.
- Saving metadata and performing final integrity checks.

The final annual forecasting datasets are organized by target year:

| Split | Target years |
|---|---|
| Training | 2010–2017 |
| Validation | 2018–2023 |
| Testing | 2024–2025 |

## Expected Model-Ready Outputs

The final preparation notebook generates the annual forecasting datasets and metadata required by the downstream model notebooks.

Expected files include:

```text
training_annual.npz
validation_annual.npz
testing_annual.npz
annual_forecasting_metadata.json
```

The NPZ files contain the following arrays:

| Array | Description |
|---|---|
| `X` | Input sequences containing normalized climate features and missingness indicators |
| `Y` | Target rainfall and maximum-temperature values |
| `Y_mask` | Observation-validity mask for the target variables |
| `input_indices` | Indices corresponding to the input years |
| `target_indices` | Indices corresponding to target years |
| `target_years` | Forecast target years |

The metadata file records information such as the forecasting task, input-window length, target-year splits, number of nodes, input features, target variables, annual coverage criterion, imputation approach, normalization statistics, and graph information.

The final spatial graph is stored separately and is used by the downstream graph-based models.

## Data and Environment Requirements

The notebooks require the source rainfall and maximum-temperature datasets, the research-node coordinate file, and the spatial graph used in the study.

File locations are defined within the notebooks and may need to be modified to match the user's environment. The original experiments were organized using a local project directory and/or Kaggle input datasets.

Before running the notebooks:

1. Obtain the required rainfall and maximum-temperature source files.
2. Place the files in the expected data directories or update the path configuration cells.
3. Provide the research-node coordinate file.
4. Provide the required spatial graph file where specified.
5. Ensure the required Python packages are installed, including the packages used for numerical processing, NetCDF handling, plotting, geospatial distance calculations, and PyTorch/PyTorch Geometric graph processing.

## Reproducibility Notes

- Run the notebooks in the order listed above.
- Update file paths for your local environment or Kaggle environment before execution.
- The model-ready preparation notebook uses chronological data splits.
- Normalization statistics are calculated from training data.
- Missing-data handling and target masks are retained to distinguish observed and imputed values.
- The annual forecasting datasets use a 10-year input window and one-year-ahead target.
- The annual coverage threshold is 90%.
- The final training, validation, and testing datasets are generated by the model-ready data preparation notebook.

## Relationship to the Main Modeling Pipeline

The outputs from this folder are used by the downstream notebooks for:

- GRU and LSTM baseline forecasting
- Spatial encoder comparison (GCN, GAT, and GATv2)
- GATv2 spatial embedding extraction
- GATv2-GRU forecasting
- Ablation and robustness experiments
- End-to-end fine-tuning experiments
- Final evaluation and visualization

This folder therefore contains the data-validation and preprocessing stages required before model training and evaluation.
