# 🌧️ RAINGUARD — AI-Based Urban Flood Early-Warning System

> **SIH26071 — AI-Based Heavy Rainfall, Flood-Risk & Early-Warning System**

RAINGUARD is an AI-assisted urban flood early-warning system designed to forecast near-term rainfall, estimate localized flood risk, and translate flood predictions into actionable impact information for city authorities.

The system combines satellite rainfall data, high-resolution terrain and land-cover information, hydrological features, and machine learning to answer:

**"Given the rainfall that has occurred or is expected, which areas are likely to flood, how severe is the risk, and who or what could be affected?"**

---

## 🚨 Problem

Urban flooding can cause severe disruption to:

- 👥 Population
- 🚧 Roads and transportation
- 🏥 Hospitals and critical infrastructure
- 🏫 Schools and public facilities

Traditional rainfall forecasts alone do not provide enough information about **where flooding is likely to occur and what the consequences may be**.

RAINGUARD bridges this gap by connecting:

**Rainfall → Flood Risk → Impact → Early Warning**

---

## 💡 Our Approach

RAINGUARD uses a two-stage AI architecture:

### 1️⃣ Rainfall Forecasting — ConvLSTM

Historical satellite rainfall observations from **NASA GPM IMERG** are used to forecast future rainfall.

```text
Historical Rainfall
        ↓
     ConvLSTM
        ↓
Future Rainfall

The rainfall model captures both the spatial and temporal behaviour of precipitation.

2️⃣ Flood-Risk Prediction — XGBoost

Predicted rainfall is combined with physical and environmental features of Chennai.

Features include:

Elevation
Slope
Flow accumulation
Topographic Wetness Index (TWI)
HAND
Distance to drainage/waterways
Land-cover characteristics
Built-up/urbanization
Soil moisture
Rainfall accumulation
Antecedent precipitation
Hydrologically informed interaction features
Future Rainfall
      +
Terrain & Geography
      +
Land Cover
      +
Soil / Hydrological Features
      ↓
   XGBoost
      ↓
Flood Probability
🗺️ Flood-Risk Mapping

The XGBoost model produces a continuous:

P(Flood) ∈ [0, 1]

which is converted into risk categories:

🟢 Low
🟡 Moderate
🟠 High
🔴 Severe

This produces a spatial flood-risk map for the study area.

🏙️ Impact Assessment

RAINGUARD goes beyond simply producing a flood map.

The predicted flood-risk areas are combined with geographic datasets to estimate:

👥 Population Exposure

Estimated population located within predicted high-risk areas.

🚧 Road Risk

Road segments intersecting predicted high-risk zones.

🏥 Critical Infrastructure

Hospitals and other critical facilities located in risk zones.

🚨 Early Warning

The system converts these results into an impact-aware warning containing information such as:

Predicted rainfall
Flood probability
Risk level
Estimated exposed population
High-risk roads
Critical infrastructure
Recommended emergency action
🔬 Historical Flood Replay

RAINGUARD includes a historical replay of the December 2015 Chennai flood event.

The system uses information available before the event, generates rainfall and flood-risk predictions, and compares the predicted flood extent with independently observed Sentinel-1 SAR flood observations.

Historical Rainfall
        ↓
Rainfall Forecast
        ↓
Flood-Risk Prediction
        ↓
Predicted Flood Map
        ↓
Compare with Sentinel-1
        ↓
Quantitative Validation

Sentinel-1 is used as ground truth/validation, rather than as a future flood prediction input.

📊 Current Results

Our current experimental results include:

Metric	Result
ConvLSTM vs Persistence	24.6% lower RMSE
Flood Recall	85.3%
Flood Precision	17.1%
Flood F1 Score	0.285
Flood IoU / CSI	0.174
Hospitals Identified at Risk	27

The system currently prioritizes high recall for early-warning purposes, while spatial precision and IoU remain areas for further optimization.

We report measured experimental results rather than claiming artificially high accuracy.

🧠 Key Technical Features
Two-Model Architecture

Separates rainfall forecasting from flood-risk prediction.

Spatially Disjoint Validation

Chennai is divided into geographically separate blocks to reduce spatial data leakage and test generalization on unseen areas.

Hydrologically-Informed Features

The flood model incorporates physical relationships such as:

Effective Runoff Volume
Compound Saturation-TWI
Hydraulic Load Index
Drainage Gradient Factor
Basin Vulnerability Index
Satellite-Based Validation

Sentinel-1 SAR observations are used to independently validate predicted flood extent.

Impact-Based Warning

The system translates flood probability into population, road, and infrastructure exposure.

🏗️ System Architecture
             NASA GPM IMERG
                    │
                    ▼
             Rainfall Data
                    │
                    ▼
                ConvLSTM
                    │
                    ▼
            Future Rainfall
                    │
          ┌─────────┴─────────┐
          │                   │
          ▼                   ▼
      Terrain              Land Cover
      DEM/Slope            Urbanization
      TWI/HAND             Waterways
          │                   │
          └─────────┬─────────┘
                    │
                    ▼
             XGBoost Model
                    │
                    ▼
           Flood Probability
                    │
                    ▼
             Flood-Risk Map
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
   Population     Roads     Infrastructure
       │            │            │
       └────────────┼────────────┘
                    ▼
            Impact Assessment
                    │
                    ▼
             Early Warning
                    │
                    ▼
            Streamlit Dashboard
🛠️ Technology Stack
Machine Learning
Python
PyTorch
ConvLSTM
XGBoost
scikit-learn
Geospatial Processing
Google Earth Engine
rasterio
xarray
GeoPandas
Shapely
PyProj
OSMnx
Data & Remote Sensing
NASA GPM IMERG
ERA5-Land
Copernicus DEM
Dynamic World / land-cover data
Sentinel-1 SAR
OpenStreetMap
Visualization & Dashboard
Matplotlib
Folium
Geemap
Streamlit
Development
Google Colab
Google Drive
📁 Project Structure
RAINGUARD/
│
├── app.py
├── README.md
│
├── features/
│   ├── chennai_terrain_features.tif
│   ├── chennai_landcover_features.tif
│   ├── chennai_flood_labels_2015.npz
│   └── ...
│
├── models/
│   ├── rainfall/
│   │   └── convlstm_rainfall_best.pth
│   │
│   └── flood/
│       ├── xgboost_flood_model.json
│       └── flood_model_features.json
│
└── outputs/
    ├── maps/
    └── reports/
🎯 Study Area

The initial implementation focuses on:

Chennai, Tamil Nadu, India

Chennai was selected because of its history of severe urban flooding, relatively flat terrain, extensive built-up areas, and availability of historical satellite observations and geospatial datasets.

⚠️ Current Limitations

RAINGUARD is a research/prototype system and should not be interpreted as an operational government warning system.

Current limitations include:

Rainfall input from IMERG has approximately 10 km spatial resolution.
The fine-resolution flood-risk map localizes risk using high-resolution geographic features; it does not create street-level rainfall observations.
The current system predicts flood probability/risk rather than exact water depth.
Flood spatial precision requires further optimization.
The current implementation focuses on Chennai rather than nationwide forecasting.
🚀 Future Work

Planned improvements include:

Real-time IMD radar / low-latency rainfall integration
Improved flood probability calibration
Multi-city generalization
Dynamic evacuation routing
Hydrodynamic modelling for water-depth estimation
Uncertainty/ensemble forecasting
SMS and push-notification alerts
Production cloud deployment
🌍 Vision

RAINGUARD aims to move flood management from:

"It is going to rain heavily."

to:

"This area is likely to experience high flood risk, approximately X people may be exposed, these roads may be affected, and these critical facilities require attention."

👥 Project

RAINGUARD

Smart India Hackathon — SIH26071

AI-Based Heavy Rainfall, Flood-Risk & Early-Warning System


### One important thing

Since you're currently editing the GitHub README, **don't put your giant ASCII architecture from your previous message into the README**. It will make the GitHub page unnecessarily huge.

The README above gives you a much cleaner structure:

**Project → Problem → Approach → Models → Impact → Results → Architecture → Tech Stack → Limitations → Future Work**

Your actual project documentation supports this structure, including the two-model architecture, Chennai historical replay, Sentinel-1 validation, impact assessment, and Streamlit dashboard. 

**For the screen you're currently on:** copy everything inside the code block, paste it into the editor, then click **"Commit changes..."**.
