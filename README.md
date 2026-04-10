# PlanktonOfSchelde (POS)

A research repository containing data, analysis code, and results on N₂O (nitrous oxide) concentrations, fluxes, and related biogeochemical variables along the **Schelde River Estuary** (Belgium/Netherlands).

![Schelde estuary landscape](https://www.belspo.be/belspo/NewRV/img/theme_01.jpg)

---

## Table of Contents

- [Overview](#overview)
- [Repository Structure](#repository-structure)
- [Data Description](#data-description)
  - [Water Chemistry (GC-DATA)](#water-chemistry-gc-data)
  - [Historical N₂O Flux Data (de Wilde & de Bie)](#historical-n₂o-flux-data-de-wilde--de-bie)
  - [Wind Speed](#wind-speed)
  - [River Discharge & Water Level](#river-discharge--water-level)
  - [Spatial Data (GIS)](#spatial-data-gis)
  - [Derived/Computed Data](#derivedcomputed-data)
- [Notebooks](#notebooks)
- [Requirements](#requirements)
- [Data Sources & Acknowledgements](#data-sources--acknowledgements)

---

## Overview

This repository supports research on greenhouse gas (N₂O) emissions and nutrient cycling in the Schelde Estuary. The main goals are:

- Measure and model N₂O concentrations and fluxes across a salinity gradient (freshwater → marine).
- Calculate river discharge from hydrodynamic velocity and cross-sectional area data.
- Compare 2023 measurements with historical data from de Wilde & de Bie (2000) to identify long-term trends (1978–2023).
- Apply steady-state models to understand the relationship between nutrients (NH₄, NOₓ) and N₂O.

---

## Repository Structure

```
PlanktonOfSchelde/
├── README.md
├── data/
│   ├── GC-DATA.csv                      # 2023 water chemistry from research cruise
│   ├── schelde_compartment_deWilde.csv  # Spatial compartment areas
│   ├── wind-speed.csv                   # Wind speed time series (March 2023)
│   ├── wind-speed.xlsx                  # Wind speed (Excel format)
│   ├── wind_schelde.nc                  # NetCDF wind field data
│   ├── Raw data/
│   │   └── Belgica-20230506T091730Z.zip # Raw data archive from R/V Belgica cruise
│   ├── SIG/                             # Spatial/GIS data (GeoPackage format)
│   │   ├── schelde.gpkg
│   │   ├── schelde-data.gpkg
│   │   └── scheld-voronoi.gpkg
│   ├── deWildedeBie/                    # Historical plankton and N₂O data
│   │   ├── Belgica2023.csv              # Merged Belgica 2023 cruise data
│   │   ├── schelde-FN2O-JUL96.csv       # N₂O flux (July 1996)
│   │   ├── schelde-FN2O-OCT78.csv       # N₂O flux (October 1978)
│   │   └── schelde-FN2O-OCT93.csv       # N₂O flux (October 1993)
│   └── discharge/                       # Hydrodynamic data from waterinfo.be
│       ├── Oosterweel-Boven SF_Zeeschelde_.csv     # Flow velocity (5-min intervals)
│       └── Antwerpen tij_Zeeschelde_Tidal waterlevel.wml2  # Tidal water level (WaterML 2.0)
└── notebooks/
    ├── SSM.ipynb                        # Steady-State Modelling
    ├── Discharge.ipynb                  # Discharge calculation at Antwerp
    ├── Comparison.ipynb                 # Comparison with historical data
    ├── flux_stations.csv                # Computed N₂O flux per station
    ├── station_kilometer.csv            # Station coordinates + river km mapping
    └── cross-section.csv                # River cross-sectional area by distance
```

---

## Data Description

### Water Chemistry (GC-DATA)

**File:** `data/GC-DATA.csv`  
**Format:** Semicolon-delimited CSV  
**Sampling:** R/V Belgica cruise – March 10–11, 2023  
**Methods:** CTD Niskin bottle or direct seawater tap

| Column | Units | Description |
|---|---|---|
| Station | — | Station identifier (e.g. S01, T01) |
| Latitude (N) | °N (WGS84) | Decimal degrees |
| Longitude (E) | °E (WGS84) | Decimal degrees |
| date | DD-MM-YYYY | Sampling date |
| Tmean (°C) | °C | Mean water temperature |
| Salinity PSU | PSU | Practical Salinity Units |
| N2O (nM) | nM | Dissolved nitrous oxide concentration |
| NH4 (µmol/L) | µmol/L | Ammonium concentration |
| NOx (µmol/L) | µmol/L | Nitrate + Nitrite concentration |
| O2 dissous winkler (mg/L) | mg/L | Dissolved oxygen (Winkler method) |
| method | — | Sampling method |

**Stations (15 total):** S01, S01B, S04, S09, S12, S15, S15B, S22, S22B, T01, T02, T04, T05, T06, T07  
**Salinity range:** 0.49–29.7 PSU (covers the full estuarine gradient)  
**Missing values:** coded as `-999`

---

### Historical N₂O Flux Data (de Wilde & de Bie)

**Directory:** `data/deWildedeBie/`

Three files contain historical N₂O flux transects along the Schelde from the landmark study by de Wilde & de Bie (2000):

| File | Sampling Period | Variables |
|---|---|---|
| `schelde-FN2O-OCT78.csv` | October 1978 | x (km from mouth), y (N₂O flux) |
| `schelde-FN2O-OCT93.csv` | October 1993 | x (km from mouth), y (N₂O flux) |
| `schelde-FN2O-JUL96.csv` | July 1996 | x (km from mouth), y (N₂O flux) |

- Flux units: µmol m⁻² day⁻¹  
- 15 data points per transect  
- Used in `Comparison.ipynb` to assess 25-to-45-year trends in N₂O emissions

**File:** `data/deWildedeBie/Belgica2023.csv`  
Contains the 2023 cruise measurements merged with spatial (compartment area) and flux data for direct comparison with the historical transects.

---

### Wind Speed

**Files:** `data/wind-speed.csv` / `data/wind-speed.xlsx` / `data/wind_schelde.nc`  
**Format:** Semicolon-delimited CSV, Excel, and NetCDF  
**Time resolution:** 15-minute intervals  
**Date range:** March 10–12, 2023

| Column | Units | Description |
|---|---|---|
| Timestamp | ISO 8601 | Date and time of measurement |
| Wind speed | m/s | Wind speed |
| Quality Code | — | 130 = good quality; 150 = lower quality |

---

### River Discharge & Water Level

**Directory:** `data/discharge/`  
**Source:** [waterinfo.be](https://www.waterinfo.be) (Flemish Water Authority)

#### Flow Velocity
**File:** `Oosterweel-Boven SF_Zeeschelde_.csv`  
**Station:** Oosterweel-Boven (Zeeschelde, near Antwerp)  
**Time resolution:** 5-minute intervals  
**Date range:** March 10–12, 2023 (577 measurements)

| Column | Units | Description |
|---|---|---|
| Timestamp | ISO 8601 | Date and time |
| Value | m/s | Flow velocity |
| Quality Code | — | 111 = good quality |

#### Tidal Water Level
**File:** `Antwerpen tij_Zeeschelde_Tidal waterlevel.wml2`  
**Station:** Antwerp (Zeeschelde)  
**Format:** WaterML 2.0 (XML)  
**Content:** Tidal water level time series

> **Disclaimer:** Discharge and water level data are provided by waterinfo.be. Usage is subject to their terms of service. See `data/discharge/Oosterweel-Boven SF_Zeeschelde_/disclaimer.txt` and `data/discharge/Antwerpen tij_Zeeschelde_Tidal waterlevel/disclaimer.txt`.

---

### Spatial Data (GIS)

**Directory:** `data/SIG/`  
**Format:** GeoPackage (`.gpkg`)  
**Coordinate system:** WGS84 (EPSG:4326)

| File | Content |
|---|---|
| `schelde.gpkg` | River geometry (polylines, stations) |
| `schelde-data.gpkg` | Attribute data linked to spatial features |
| `scheld-voronoi.gpkg` | Voronoi polygons for spatial interpolation |

**File:** `data/schelde_compartment_deWilde.csv`  
Contains 11 spatial compartments of the Schelde with their areas (m²), used for flux budget calculations.

---

### Derived/Computed Data

These files are outputs generated by the notebooks:

| File | Description |
|---|---|
| `notebooks/station_kilometer.csv` | Station coordinates mapped to river kilometer distance |
| `notebooks/flux_stations.csv` | Calculated N₂O flux (µmol m⁻² hour⁻¹) per station |
| `notebooks/cross-section.csv` | River cross-sectional area as a function of distance (km) |

---

## Notebooks

### `SSM.ipynb` – Steady-State Modelling
Applies a steady-state model to N₂O and nutrient data. Plots concentration vs. salinity relationships and compares measured vs. modeled distributions.

### `Discharge.ipynb` – Discharge at Antwerp
Computes river discharge (Q = A × V) from flow velocity measurements (Oosterweel station) and cross-sectional area data. Produces time series plots of discharge during the sampling period.

### `Comparison.ipynb` – Comparison with Historical Data
Overlays 2023 N₂O flux measurements with the three historical de Wilde & de Bie transects (1978, 1993, 1996) to assess long-term trends in N₂O emissions from the Schelde estuary.

---

## Requirements

The notebooks use standard scientific Python libraries:

```
numpy
pandas
matplotlib
```

To install:
```bash
pip install numpy pandas matplotlib
```

NetCDF and GIS data may additionally require:
```bash
pip install netCDF4 geopandas
```

---

## Data Sources & Acknowledgements

- **Waterinfo.be** – Flow velocity and tidal water level data (Vlaamse Milieu Maatschappij / De Vlaamse Waterweg)
- **de Wilde & de Bie (2000)** – Historical N₂O flux measurements on the Schelde estuary
- **R/V Belgica** – Research vessel providing access to the estuary for the 2023 sampling campaign
- **Belspo** – RV Belgica Picture
