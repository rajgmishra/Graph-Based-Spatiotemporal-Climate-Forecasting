# Rainfall Dataset

This folder is intended to store the India Meteorological Department (IMD) gridded rainfall datasets used in this study.

## Dataset Source

Indian Meteorological Department (IMD)

Rainfall Data Portal:

https://www.imdpune.gov.in/cmpg/Griddata/Rainfall_25_NetCDF.html

## Expected Files

Download the annual rainfall NetCDF files covering the study period (2000–2023) and place them in this directory.

Example:

```text
RF25_ind2000_rfp25.nc
RF25_ind2001_rfp25.nc
...
RF25_ind2023_rfp25.nc
```

## Data Description

* Variable: Rainfall
* Spatial Resolution: 0.25° × 0.25°
* Format: NetCDF (.nc)
* Region: India
* Temporal Coverage Used in This Study: 2000–2023

## Sample Data

A sample rainfall file may be provided in:

```text
data/sample_data/
```

to demonstrate the required file format and structure.

## Notes

The notebooks expect all rainfall files to be available in this folder before execution of:

```text
1_build_static_graph.ipynb
```
