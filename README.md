# 🗺️ Land Use Change Prediction Using Cellular Automata
### Kabupaten Semarang | 2017–2019 → 2021 Projection

![MOLUSCE](https://img.shields.io/badge/Tool-NextGIS%20MOLUSCE-blue)
![QGIS](https://img.shields.io/badge/Platform-QGIS-green)
![Method](https://img.shields.io/badge/Method-ANN%20%2B%20Cellular%20Automata-orange)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

---

## 📌 Project Overview

This project models land use and land cover (LULC) change in **Kabupaten Semarang, Central Java, Indonesia** using the **Cellular Automata – Artificial Neural Network (CA-ANN)** method. The analysis was carried out as part of a university practicum task using the **NextGIS MOLUSCE plugin** in QGIS.

The model learns spatial transition patterns between two historical land cover periods (2017 and 2019), then simulates and predicts land cover for a future period. The prediction result is validated against an actual 2021 land cover map to assess model accuracy.

### 💡 Beyond the Practicum Task

The original practicum task only required **proximity to buildings** and **proximity to roads** as variable layers. During initial training, the model produced a poor result — Kappa of **0.495** with no sign of convergence in the learning curve — indicating the two variables alone were insufficient to explain land cover transitions in this study area.

To address this, **DEM and slope layers were independently added** based on the reasoning that terrain characteristics are fundamental physical constraints on land use change, particularly in Kabupaten Semarang where the landscape ranges from lowland village flats in the north to hilly terrain in the south. This decision was made without guidance from the practicum task brief.

After adding the terrain variables and retuning the model parameters, the Kappa improved to **0.816** — a jump of more than 0.32 — with a clean convergence pattern in the learning curve. This result demonstrates that variable selection has a greater impact on model quality than hyperparameter tuning alone.

---

## 🎯 Objectives

1. Identify land use and land cover changes in Kabupaten Semarang between 2017 and 2019
2. Model the transition potential of each land cover class using the ANN method
3. Simulate future land cover conditions using Cellular Automata
4. Validate the simulated 2021 land cover against the actual 2021 land cover map

---

## 🗂️ Study Area

| Item | Detail |
|---|---|
| Location | Kabupaten Semarang, Central Java, Indonesia |
| Analysis Period | 2017 – 2019 (transition) → 2021 (validation) |
| Land Cover Classes | Water, Vegetation, Land |

---

## 📁 Repository Structure

```
ca-landuse-semarang/
│
├── README.md
│
├── data/
│   ├── lulc.zip                # zipped file of base year, second year, and actual land cover (for input and validation)
│   │
│   └── variables.zip          # Proximity to building and road, DEM and slope raster as factor variables
│
├── output/
│   ├── transition_potential.zip
│   │
│   └── simulation_validation.zip
│
├── maps/
│   ├── LULC_2017_map.png
│   ├── LULC_2019_map.png
│   ├── LULC_2021_actual_map.png
│   ├── LULC_2021_predicted_map.png
│   └── transition_LULC_maps.png
```

---

## 🛰️ Data Sources

| Layer | Source | Year |
|---|---|---|---|
| Land Cover 2017 | *University practicum data* | 2017 |
| Land Cover 2019 | *University practicum data* | 2019 |
| Land Cover 2021 | *University practicum data* | 2021 |
| Building footprint | *Derived — proximity raster generated in QGIS* | 2019 |
| Road network | *Derived — proximity raster generated in QGIS* | 2019 |
| DEM | *DEMNAS BIG* | 2014-2018 | 
| Slope | Derived from DEM using QGIS Slope tool | 2014-2018 |

> ⚠️ **Note:** Land cover raster layers were provided by the university assistant as part of practicum materials. The original data source is unconfirmed.

---

## ⚙️ Methodology

### Software & Tools
- **QGIS** — spatial data processing and visualization
- **NextGIS MOLUSCE Plugin** — transition potential modelling and CA simulation
- **ArcMap** — pre-processing and DEM mosaicking (4 scenes merged to 1)

### Workflow

```
1. DATA PREPARATION
   Land Cover 2017 + 2019 ──────────────────────────┐
   Variable Layers (proximity, DEM, slope) ─────────┤
                                                    ▼
                                             Geometry Check
                                                    |
                                                    ▼
                                      Evaluating Correlation Tab
                        (Check for multicollinearity between variable layers)
                                                    |
                                                    ▼
                                            Area Changes Tab
                              (Quantify LULC change between 2017 and 2019)
                                                    |
                                                    ▼
                                    Transition Potential Modelling          
                                          (ANN (MLP) Training)
                                                    |
                                                    ▼
                                           CA Simulation Tab
                                 (Simulate land cover for 2021 based on:
                                       - Transition potential maps
                                       - Area change demand matrix)
                                                    |
                                                    ▼
                                             Validation Tab
                           (Compare predicted 2021 vs actual 2021 land cover
                     Output: Kappa coefficient, Overall Accuracy, Figure of Merit)
```

---

## 🤖 ANN Parameters

### Model Iteration Log

Two model configurations were tested. The improvement between iterations came primarily from adding terrain variables, not from parameter changes alone.

| Parameter | Initial Run | Final Run |
|---|---|---|
| Variable Layers | Road + Building only | Road + Building + DEM + Slope |
| Sampling Mode | Stratified | Random |
| Number of Samples | 2000 | 5000 |
| Neighbourhood | 1 px | 3 px |
| Learning Rate | 0.001 | 0.001 |
| Maximum Iterations | 1000 | 1000 |
| Hidden Layers | 6 | 5 |
| Momentum | 0.100 | 0.100 |

### Model Performance Comparison

| Metric | Initial Run | Final Run |
|---|---|---|
| Current Validation Kappa | 0.49543 | **0.81621** |
| Min Validation Overall Error | 0.23150 | **0.06959** |
| Δ Overall Accuracy | -0.04226 | -0.00697 |
| Learning Curve | Oscillating, no convergence | Sharp drop, stable convergence |

> Kappa of **0.816** indicates *almost perfect agreement* between predicted and reference transitions, which is considered strong for land use transition modelling. The improvement in **+0.32 Kappa** between runs was primarily driven by the addition of DEM and slope as variable layers, rather than hyperparameter tuning.

---

## 🗺️ Variable Layers

The practicum task specified two variable layers. Two additional terrain layers were independently introduced after the initial model failed to converge adequately.

### Required by Practicum Task

| Variable | Role in Model | Rationale |
|---|---|---|
| Proximity to Road | Accessibility driver | Areas near roads face higher development pressure |
| Proximity to Building | Urban fringe driver | Existing buildings predict neighbourhood expansion |

### Added Independently to Improve Model Performance

| Variable | Role in Model | Rationale |
|---|---|---|
| Slope | Physical constraint | Steep terrain limits land conversion — critical for distinguishing convertible vs non-convertible vegetated areas in Kabupaten Semarang's hilly southern zone |
| DEM | Terrain context | Elevation zones influence land use patterns — the contrast between northern lowlands and southern highlands is a defining spatial characteristic of this study area |

> **Why these were added:** Training with only road and building proximity produced a Kappa of 0.495 and an unstable learning curve with no convergence. The two proximity variables capture development pressure but provide no information about physical constraints — meaning the model could not explain why some accessible areas convert while others do not. Adding terrain variables resolved this by giving the ANN the physical context needed to discriminate between transition and non-transition pixels. Kappa improved from 0.495 to 0.816 after this change.

---

## 📊 Results

### Land Cover Area Change (2017–2019)

| Land Cover Class | Area 2017 (Sq.Km) | Area 2019 (Sq.Km) | Change (Sq.Km) |
|---|---|---|---|
| Water | 16,54 | 13,25 | 3,29 |
| Vegetation | 401,62 | 291,25 | 110,37 |
| Land | 599,14 | 712,80 | 113,66 |


### Validation Results (Predicted 2021 vs Actual 2021)

| Metric | Value |
|---|---|
|Validation iterations | 5 |
| Kappa (overall) | 0,648 |
| Kappa (histogram) | 0,723 |
| Kappa (location) | 0,896 |


---

## 🖼️ Maps

### Land Cover 2017
![Map LULC 2017]()

### Land Cover 2019
![Map LULC 2019]()

### Land Cover 2021
![Map LULC 2021]()

### Transition Potential Maps
![Transition Potential Map]()

### Predicted Land Cover 2021
![Predicted Map LULC 2021]()

### Certainty Map
![Map of Certainty]()

---

## 📝 Key Findings

- The dominant land cover transition between 2017 and 2019 in Kabupaten Semarang was **Vegetation → Land**, covering approximately **122,612700 Sq. Km**.
- The CA simulation produced a predicted 2021 land cover with a Kappa of **0,648** when validated against the actual 2021 map, indicating **83,8** predictive accuracy.

---

## ⚠️ Limitations & Honest Notes

- Land cover source data could not be fully traced — the original provider is unconfirmed beyond university practicum materials
- Only 3 land cover classes were used (water, vegetation, land) — finer classification would improve model specificity
- The model does not incorporate policy constraints such as RTRW spatial planning zones
- DEM and slope carry overlapping information as slope is derived directly from the DEM — their correlation was checked in MOLUSCE's Evaluating Correlation tab before inclusion. Despite partial redundancy, both were retained as each contributes a distinct spatial signal (absolute elevation vs local gradient)
- The original practicum task only required road and building proximity variables — DEM and slope were added independently based on model diagnostics, not task specification. This is documented transparently above.

---

## 📚 References

- Eastman, J.R. (2006). *IDRISI Andes: Guide to GIS and Image Processing.* Clark University.
- Pontius, R.G. & Schneider, L.C. (2001). Land-cover change model validation by an ROC method for the Iguapé watershed, Brazil. *Agriculture, Ecosystems & Environment*, 85(1–3), 223–243.
- NextGIS MOLUSCE Plugin Documentation. Retrieved from https://nextgis.com/blog/molusce/
- Abbas, Z., Yang, G., Zhong, Y., & Zhao, Y. (2021). Spatiotemporal Change Analysis and Future Scenario of LULC Using the CA-ANN Approach: A Case Study of the Greater Bay Area, China. Land. https://doi.org/10.3390/land10060584
- *(Add your university practicum handout or guidebook as a reference if available)*

---

## 👤 Author

**Nisrina Azza Salma**
Geodetic Engineering
Gadjah Mada University

*Practicum Task — Geospatial Analysis — 6th Semester/2023*

---

*This project was completed as part of university practicum coursework. All spatial analysis was conducted using QGIS and the NextGIS MOLUSCE plugin without custom programming.*
