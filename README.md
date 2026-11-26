# Graph-Based Spatiotemporal Climate Forecasting

This repository provides the notebooks and supporting files for research on annual rainfall and maximum-temperature forecasting over India using recurrent and graph-based learning models.

## Repository Contents

| Folder / File | Description |
|---|---|
| `data/` | Documentation for the IMD rainfall and maximum-temperature source data. |
| `notebooks/Data Preparation/` | Notebooks for data validation, integration, and preparation. |
| `notebooks/Spatiotemporal_GNN_Kaggle_Data/` | Prepared annual datasets, metadata, and spatial graph. |
| `notebooks/` | Model training, evaluation, sensitivity, and visualization notebooks, including the HiSTGNN-adapted experiment. |
| `output/` | Selected experimental summaries in CSV and TXT formats. |
| `requirements.txt` | Python package dependencies. |
| `LICENSE` | MIT License. |

See the [notebooks README](notebooks/README.md) for the notebook list and execution guidance. Folder-specific documentation is available in the relevant directories.

## Data

The source climate data are obtained from the [India Meteorological Department (IMD)](https://www.imdpune.gov.in/). The study data cover 2000–2025.

- [Rainfall data details](data/rainfall/README.md)
- [Maximum-temperature data details](data/temperature/README.md)

The raw IMD files are not included in this repository. The prepared annual datasets and spatial graph are available in `notebooks/Spatiotemporal_GNN_Kaggle_Data/`.

## Setup

Create and activate a virtual environment, then install the listed dependencies.

```bash
python -m venv climate_env
```

Windows:

```bash
climate_env\\Scripts\\activate
```

Linux / macOS:

```bash
source climate_env/bin/activate
```

Install packages:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

For GPU execution, install a PyTorch build compatible with the local CUDA environment. See the [official PyTorch installation guide](https://pytorch.org/get-started/locally/) for platform-specific instructions.

## Running the Notebooks

You can use the prepared datasets or recreate them from the original IMD files. For the latter, run the notebooks in `notebooks/Data Preparation/` in their listed order.

Several notebooks were developed in Kaggle and may contain environment-specific file paths. Review and adjust input and output paths before running them locally.

## Citation and License

For citation details, refer to the associated research publication:

*Graph-Based Spatiotemporal Modeling for Rainfall and Temperature Forecasting.*

This repository is released under the [MIT License](LICENSE).
