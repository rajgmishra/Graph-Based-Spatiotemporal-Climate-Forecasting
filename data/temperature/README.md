# IMD Gridded Maximum Temperature Dataset

This directory documents the raw maximum-temperature data required for the rainfall and temperature forecasting study. The raw IMD source files are downloaded separately and are not included in this GitHub directory.

## Dataset Source

India Meteorological Department (IMD) — Pune

[IMD Gridded Daily Maximum Temperature Data](https://www.imdpune.gov.in/cmpg/Griddata/Max_1_Bin.html)

## Temporal Coverage and File Formats

The study uses **daily maximum-temperature data from 1 January 2000 to 31 December 2025**. The source files use two formats:

| Years | Number of annual files | File format | Expected filename pattern |
|---|---:|---|---|
| 2000–2023 | 24 | NetCDF (`.nc`) | `Maxtemp_MaxT_YYYY.nc` |
| 2024–2025 | 2 | Binary GRD (`.GRD`) | `Maxtemp_MaxT_YYYY.GRD` |
| **Total** | **26** | Mixed | — |

Expected filenames include:

```text
Maxtemp_MaxT_2000.nc
Maxtemp_MaxT_2001.nc
...
Maxtemp_MaxT_2023.nc
Maxtemp_MaxT_2024.GRD
Maxtemp_MaxT_2025.GRD
```

The extension is important: the 2024 and 2025 source files are GRD files, not NetCDF files.

## Data Description

| Property | Description |
|---|---|
| Source | India Meteorological Department (IMD) |
| Variable | Daily maximum temperature |
| Units | Degrees Celsius (°C) |
| Temporal resolution | Daily |
| Native spatial resolution | 1° × 1° |
| Native grid dimensions | 31 × 31 |
| Approximate coordinate extent | 7.5°–37.5° N, 67.5°–97.5° E |
| Region | India |
| File formats | NetCDF for 2000–2023; GRD for 2024–2025 |
| Study period | 2000–2025 (26 years) |

The inspected NetCDF files contain a `temperature` variable with `time`, `lat`, and `lon` dimensions. The GRD files are read in the current validation workflow as little-endian 32-bit floating-point values and reshaped to daily arrays of 31 × 31 cells. The expected number of daily records is 365 or 366 depending on the year.

For the GRD files, the value `99.9` is treated as the missing-data marker and converted to NaN during preprocessing.

## Spatial Alignment and Preprocessing

The source temperature grid has a native resolution of 1° × 1°. During preprocessing, temperature observations are bilinearly interpolated to the **5,040 research-node coordinates** used in the forecasting dataset so they can be aligned with the rainfall observations.

The current interpolation workflow uses the four surrounding source-grid cells. An interpolated value is retained only when all four surrounding values are valid; otherwise, the result is marked as missing. The `99.9` GRD missing-data marker is converted to NaN before interpolation.

The resulting temperature data are aligned with the rainfall data at the research nodes and are subsequently used to prepare the annual forecasting inputs and targets.

## Repository Location and Notebook Path

The directory represented by this README is:

```text
data/temperature/
```

**Path configuration note:** the current temperature validation notebook (`notebooks/Data Preparation/2_temperature_data_validation.ipynb`) initially sets its input directory to `data/raw/temperature/`. This differs from the directory used in the current repository structure. Before running the notebook, either place the files under the configured `data/raw/temperature/` path or change the notebook's `TEMPERATURE_DIR` setting to point to `data/temperature/`.

## Preprocessing Workflow

Use [`2_temperature_data_validation.ipynb`](../../notebooks/Data%20Preparation/2_temperature_data_validation.ipynb) to inspect the source temperature files and validate their temporal and spatial coverage. The notebook examines the NetCDF and GRD inputs, checks missing-value patterns, and supports interpolation to the research-node coordinates.

Continue with the integration and model-ready preparation notebooks listed in [the Data Preparation README](../../notebooks/Data%20Preparation/README_Data_Preparation.md).

## Notebook Compatibility Note

The initial file-inventory section of the current temperature validation notebook identifies **24 NetCDF files (2000–2023) and two GRD files (2024–2025)**. Some later cells and saved outputs still refer to GRD files for earlier years (for example, `Maxtemp_MaxT_2000.GRD`) or report a 26-file GRD inventory. Those later cells appear to be from an earlier all-GRD workflow and should be reconciled with the mixed-format file inventory before running the entire notebook sequentially.

## Notes

- Keep the original NetCDF and GRD source files unchanged.
- Confirm the filenames and file formats before running preprocessing.
- The repository currently contains this README but does not include the raw temperature files. Users must obtain the source data from IMD.
