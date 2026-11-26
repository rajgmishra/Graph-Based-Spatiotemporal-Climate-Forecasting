# IMD Gridded Rainfall Dataset

This directory documents the raw rainfall data required for the rainfall and temperature forecasting study. The raw IMD source files are downloaded separately and are not included in this GitHub directory.

## Dataset Source

India Meteorological Department (IMD) — Pune

[IMD Gridded Rainfall Data (0.25° × 0.25°)](https://www.imdpune.gov.in/cmpg/Griddata/Rainfall_25_NetCDF.html)

## Temporal Coverage

The study uses **26 years of daily rainfall data, from 1 January 2000 to 31 December 2025**.

Download one annual NetCDF file for each year from 2000 through 2025.

Expected filename pattern:

```text
RF25_ind2000_rfp25.nc
RF25_ind2001_rfp25.nc
...
RF25_ind2024_rfp25.nc
RF25_ind2025_rfp25.nc
```

There should be **26 annual NetCDF files** in total.

## Data Description

| Property | Description |
|---|---|
| Source | India Meteorological Department (IMD) |
| Variable name | `RAINFALL` |
| Temporal resolution | Daily |
| Spatial resolution | 0.25° × 0.25° |
| Region | India |
| File format | NetCDF (`.nc`) |
| Study period | 2000–2025 (26 years) |
| Main dimensions | `TIME`, `LATITUDE`, `LONGITUDE` |

For example, the inspected 2000 file has dimensions `TIME=366`, `LATITUDE=129`, and `LONGITUDE=135`. The time dimension varies by year because leap years contain 366 daily observations.

## Repository Location and Notebook Path

The directory represented by this README is:

```text
data/rainfall/
```

**Path configuration note:** the current rainfall validation notebook (`notebooks/Data Preparation/1_rainfall_data_validation.ipynb`) initially sets its input directory to `data/raw/rainfall/`. This differs from the directory used in the current repository structure. Before running the notebook, either place the files under the configured `data/raw/rainfall/` path or change the notebook's `RAINFALL_DIR` setting to point to `data/rainfall/`.

## Preprocessing Workflow

The rainfall files are inspected and validated in the data-preparation workflow:

1. Check the annual NetCDF file inventory and time coverage.
2. Inspect the `RAINFALL` variable, dimensions, coordinates, and missing observations.
3. Check the spatial grid and its correspondence with the study's research-node coordinates.
4. Prepare the rainfall data for integration with maximum-temperature observations and subsequent annual forecasting.

Start with [`1_rainfall_data_validation.ipynb`](../../notebooks/Data%20Preparation/1_rainfall_data_validation.ipynb). Continue with the remaining notebooks listed in [the Data Preparation README](../../notebooks/Data%20Preparation/README_Data_Preparation.md).

## Notes

- Download all 26 annual files (2000–2025) from the IMD portal before running the rainfall validation and integration workflow.
- Keep the original NetCDF files unchanged; perform preprocessing on derived data.
- The repository currently contains this README but does not include the raw rainfall files. Users must obtain the source data from IMD.
