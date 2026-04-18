# Water Balance Analysis — Peninsular India (2000–2024)

A collection of Jupyter notebooks for analyzing the terrestrial water balance of Peninsular India using satellite and reanalysis datasets. The project covers all major water cycle components — precipitation, evapotranspiration, soil moisture, runoff, and groundwater — and closes the water budget using two independent approaches (GLDAS and GRACE).

\---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Repository Structure](#repository-structure)
3. [Datasets](#datasets)
4. [Notebooks](#notebooks)
5. [Methodology](#methodology)
6. [Dependencies](#dependencies)
7. [Setup \& Usage](#setup--usage)
8. [Water Balance Equation](#water-balance-equation)
9. [Notes \& Known Issues](#notes--known-issues)

\---

## Project Overview

This project was developed for the course **GNR644 – Water Balance** and analyses monthly hydro-climatic variables across Peninsular India from 2000 to 2024. Each water cycle component is processed independently — checking spatial resolution, resampling to a common 0.25° grid, clipping to the study region, computing monthly climatologies, and running pixel-wise Mann–Kendall trend tests. The two water balance notebooks then combine all components to compute the water budget residual.

\---

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


## Datasets

|Variable|Product|Source|Period|Native Resolution|Units|
|-|-|-|-|-|-|
|Precipitation|GPCP v3 Monthly|NASA / NOAA|2000–2024|0.5°|mm/day|
|Evapotranspiration|GLEAM v3|gleam.eu|2000–2024|0.25°|mm/month|
|Soil Moisture|(satellite-based)|—|2000–2024|variable|m³/m³|
|Runoff|ERA5 / reanalysis|ECMWF|2000–2024|\~0.25°|m|
|Groundwater (GLDAS)|GLDAS-2 Noah|NASA GSFC|2003–2024|0.25°|cm|

All datasets are stored as NetCDF (`.nc`) files and accessed from Google Drive inside Google Colab.

The study region shapefile (`peninsularIndia\_lvl3.shp`) is in EPSG:4326 (WGS84) and is used for clipping rasters to Peninsular India.

\---

## Notebooks

Final Notebooks:

### 1\. `Precipitation.ipynb`

Processes monthly rainfall from the GPCP dataset.

* Checks native resolution (`lat\_res`, `lon\_res`)
* Resamples to 0.25° grid using nearest-neighbour interpolation
* Clips to Peninsular India using the level-3 shapefile
* Computes and maps monthly climatology (Jan–Dec, 2000–2024)
* Runs pixel-wise **Mann–Kendall trend test** for each calendar month, producing maps of trend direction, Z-score, p-value, and Sen's slope

**Key variable:** `satellite\_precip` (mm/day → converted to mm/month in Water Balance notebooks)

\---

### 2\. `Evapotranspiration.ipynb`

Processes monthly actual evapotranspiration from GLEAM.

* Checks native resolution
* Resamples to 0.25° using nearest-neighbour interpolation and saves the result
* Plots monthly climatology with a `YlGnBu` colormap
* Runs pixel-wise Mann–Kendall trend test for all 12 calendar months

**Key variable:** `ET` (mm/month)

\---

### 3\. `Soil\_Moisture.ipynb`

Processes monthly surface/root-zone soil moisture.

* Checks native resolution
* Assigns CRS (`EPSG:4326`) and clips to Peninsular India
* Plots monthly climatology with a `YlGnBu` colormap
* Runs pixel-wise Mann–Kendall trend test for each calendar month

**Key variable:** `sm` (m³/m³ → converted to mm using a 1 m root-zone depth assumption in Water Balance notebooks)

\---

### 4\. `Runoff.ipynb`

Processes monthly total runoff.

* Checks native resolution (note: dimension names are `latitude`/`longitude`/`valid\_time` — distinct from other datasets)
* Clips to Peninsular India; saves cropped file
* Plots monthly climatology
* Runs pixel-wise Mann–Kendall trend test for each calendar month

**Key variable:** `ro` (m → converted to mm in Water Balance notebooks)

\---

### 5\. `Groundwater\_GLDAS.ipynb`

Processes monthly groundwater storage anomalies from GLDAS-Noah.

* Checks native resolution
* Plots monthly climatology with a `viridis` colormap
* Runs pixel-wise Mann–Kendall trend test for each calendar month

**Key variable:** `GWS\_tavg` (cm → converted to mm in Water Balance notebooks)

> ⚠️ This notebook covers 2003–2024 (GLDAS groundwater availability), which is shorter than the other component datasets.

\---

### 6\. `Water\_Balance\_GLDAS.ipynb`

Closes the water budget using **GLDAS groundwater** as the storage change component.

**Workflow:**

1. Loads all five component datasets
2. Renames dimension names in the runoff dataset to match common convention (`lat`, `lon`, `time`)
3. Sorts all datasets by `lat` and `lon`
4. Unit harmonisation — converts all variables to **mm/month**:

   * Precip: mm/day × days-in-month
   * ET: already mm/month
   * Runoff: m × 1000
   * Groundwater: cm × 10
   * Soil moisture: m³/m³ × root\_depth(1 m) × 1000
5. Computes total storage as `ΔS = Δ(SM + GWS)`
6. Aligns all datasets to the same spatial grid (interpolated to precipitation grid) and temporal axis (`inner` join)
7. Calculates the **residual**: `Residual = P − (ET + Q + ΔS)`
8. Maps the mean spatial residual
9. Plots the spatially-averaged residual time series

\---

### 7\. `Correlation Lag Analysis`

* We performed correlation of all the hydrological variables with each other.
* We performed Temporal Lag Correlation analysis and prepared a heat map.

\---

## Methodology

### Common Analytical Steps (all component notebooks)

**Step 1 — Resolution Check**
Print dataset metadata and compute `lat\_res` and `lon\_res` from coordinate arrays to confirm native resolution.

**Step 2 — Resampling / Clipping**
Most datasets are interpolated to a common 0.25° grid using `xr.DataArray.interp(..., method="nearest")`. They are then clipped to Peninsular India using `rioxarray`'s `.rio.clip()` with the shapefile geometry.

**Step 3 — Monthly Climatology**

```python
monthly\_mean = variable.groupby("time.month").mean(dim="time")
```

Plotted as a 3×4 subplot grid (one panel per month) using `cartopy` with `PlateCarree` projection, coastlines, and country borders.

**Step 4 — Mann–Kendall Trend Test**
A non-parametric monotonic trend test is applied pixel-by-pixel for each of the 12 calendar months:

```python
result = mk.original\_test(time\_series)
# returns: trend direction, Z-score, p-value, Sen's slope
```

`xr.apply\_ufunc` is used with `vectorize=True` to efficiently apply the test over the spatial grid. Results are saved as four separate arrays per month (trend, Z, p, slope) and plotted as maps.

\---

## Dependencies

Install all required packages in Google Colab with:

```bash
pip install rioxarray geopandas cartopy pymannkendall
pip install xarray netcdf4 matplotlib pandas numpy
```

|Package|Purpose|
|-|-|
|`xarray`|NetCDF I/O and labelled array operations|
|`rioxarray`|Spatial CRS assignment and raster clipping|
|`geopandas`|Shapefile reading and CRS management|
|`cartopy`|Geographic map projections and features|
|`pymannkendall`|Mann–Kendall and Sen's slope trend tests|
|`numpy`|Numerical operations|
|`matplotlib`|Plotting|
|`pandas`|Time axis manipulation (GRACE notebook)|
|`scipy`|`linregress` (imported but available for supplementary linear trend)|
|`netcdf4`|NetCDF backend for xarray|

\---

## Setup \& Usage

These notebooks are designed to run in **Google Colab** with data stored in **Google Drive**.

1. Mount Google Drive at the start of each notebook:

```python
   from google.colab import drive
   drive.mount('/content/drive')
   ```

2. Ensure your Drive folder matches this path structure:

```
   MyDrive/GNR644 - Water Balance/Data/
   ```

3. Run the component notebooks first (in any order):

   * `Precipitation.ipynb`
   * `Evapotranspiration.ipynb`
   * `Soil\_Moisture.ipynb`
   * `Runoff.ipynb`
   * `Groundwater\_GLDAS.ipynb`
4. Then run the water balance notebooks:

   * `Water\_Balance\_GLDAS.ipynb`

> ⚠️ The water balance notebooks load pre-processed (resampled/cropped) files produced by the component notebooks. Run those first, or ensure the intermediate `.nc` files already exist in Drive.

\---

## Water Balance Equation

The water budget is computed as:

```
P = ET + Q + ΔS + ε
```

Where:

|Symbol|Description|Source|
|-|-|-|
|P|Precipitation|GPCP|
|ET|Evapotranspiration|GLEAM|
|Q|Runoff|ERA5|
|ΔS|Change in storage (ΔSM + ΔGWS)|GLDAS or GRACE|
|ε|Residual (closure error)|Computed|

The residual `ε = P − (ET + Q + ΔS)` is mapped spatially and plotted as a time series to assess water budget closure.

\---

## Notes \& Known Issues

* **Dimension name mismatch:** The Runoff dataset uses `latitude`, `longitude`, and `valid\_time` as dimension names. Both water balance notebooks rename these to `lat`, `lon`, and `time` before alignment.
* **GRACE temporal gaps:** GRACE and GRACE-FO have missing months (instrument gaps). The GRACE water balance notebook uses `.reindex(..., method="nearest")` and an `inner` time join to handle this cleanly.
* **Soil moisture unit assumption:** Soil moisture (m³/m³) is converted to mm by multiplying by a fixed root-zone depth of 1 m. This is a simplifying assumption; actual rooting depth varies across land cover types.
* **GLDAS groundwater period:** GLDAS groundwater data starts in 2003. The GLDAS water balance analysis therefore covers 2003–2024, not the full 2000–2024 period of other variables.
* **Spatial resolution of GRACE:** GRACE data has a much coarser native resolution (\~1°) compared to the 0.25° used for other variables. In `Water\_Balance\_GRACE.ipynb`, all variables are interpolated to the GRACE grid, which reduces spatial detail in the residual maps.
* **Colormap choices:** `YlGnBu` is used for ET and soil moisture (both moisture-related); `viridis` for precipitation, runoff, and groundwater; `RdBu` (diverging) for the water balance residual maps.

