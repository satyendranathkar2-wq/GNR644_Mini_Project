# Water Balance Analysis — Peninsular India (2000–2024)

A collection of Jupyter notebooks for analyzing the terrestrial water balance of Peninsular India using satellite and reanalysis datasets. The project covers all major water cycle components — precipitation, evapotranspiration, soil moisture, runoff, and groundwater, and closes the water budget using two independent approaches (GLDAS and GRACE).

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Repository Structure](#repository-structure)
3. [Datasets](#datasets)
4. [Notebooks](#notebooks)
5. [Methodology](#methodology)
6. [Dependencies](#dependencies)
7. [Setup & Usage](#setup--usage)
8. [Water Balance Equation](#water-balance-equation)
9. [Notes & Known Issues](#notes--known-issues)

---

## Project Overview

This project was developed for the course **GNR644 – Water Balance** and analyses monthly hydro-climatic variables across Peninsular India from 2000 to 2024.

---

## Repository Structure

```
GNR644-Water-Balance/
│
├── Checkpoint_2/
│   ├── Copy_of_GNR644_Data_Processing.ipynb
│   ├── Copy_of_GNR644_Data_Visualization.ipynb
│   ├── Group2_GNR644_Checkpoint2_*.ipynb
│   └── Resampling.ipynb
│
├── Checkpoint_3/
│   ├── Correlation_Lag_Analysis.ipynb
│   ├── ET_SM_TSA.ipynb
│   ├── GLDAS_GW_TSA.ipynb
│   ├── GRACE_GW_TSA.ipynb
│   ├── PP_RO_TSA.ipynb
│   └── Group2_GNR644_Checkpoint3_*.ipynb
│
├── Checkpoint_4/
│   ├── Group2_GNR644_Checkpoint4_*.ipynb
│   └── Water_Balance.ipynb
│
├── Data/
│   ├── GLEAM_Evapotranspiration_resampled_NN.nc
│   ├── Rainfall_Resample_Cropped.nc
│   ├── SoilMoisture_Crop.nc
│   ├── Runoff_cropped.nc
│   ├── PENINSULAR_INDIA_GLDAS_GWS_cm_Monthly_2003_2024.nc
│   │
│   └── PeninsularIndia/
│       ├── peninsularIndia_lvl3.shp
│       ├── peninsularIndia_lvl3.dbf
│       ├── peninsularIndia_lvl3.prj
│       ├── peninsularIndia_lvl3.shx
│       └── (other shapefile components)
│
├── Final_Notebooks/
│   ├── Correlation_Lag_Analysis.ipynb
│   ├── Evapotranspiration.ipynb
│   ├── Groundwater_GLDAS.ipynb
│   ├── Precipitation.ipynb
│   ├── Runoff.ipynb
│   ├── Soil Moisture.ipynb
│   └── Water Balance_GRACE.ipynb
│
└── README.md
```

---

## Dependencies

```bash
pip install rioxarray geopandas cartopy pymannkendall
pip install xarray netcdf4 matplotlib pandas numpy
```

---

## Author

Rishi Ganesh L  
M.Tech Remote Sensing  
IIT Bombay  
