# Maximum Temperature Dataset

This folder is intended to store the India Meteorological Department (IMD) gridded maximum temperature datasets used in this study.

## Dataset Source

Indian Meteorological Department (IMD)

Maximum Temperature Data Portal:

https://www.imdpune.gov.in/cmpg/Griddata/Max_1_Bin.html

## Expected Files

Download the annual maximum temperature files covering the study period (2000–2023) and place them in this directory.

Example:

```text
Maxtemp_MaxT_2000.nc
Maxtemp_MaxT_2001.nc
...
Maxtemp_MaxT_2023.nc
```

(Actual filenames may differ depending on the IMD download package.)

## Data Description

* Variable: Maximum Temperature
* Format: Binary / NetCDF (depending on IMD distribution)
* Region: India
* Temporal Coverage Used in This Study: 2000–2023

## Preprocessing

Temperature data are interpolated to match the rainfall grid resolution (0.25° × 0.25°) during preprocessing as described in the manuscript.

## Sample Data

A sample temperature file may be provided in:

```text
data/sample_data/
```

to demonstrate the required file format and structure.

## Notes

The notebooks expect all temperature files to be available in this folder before execution of:

```text
1_build_static_graph.ipynb
```
