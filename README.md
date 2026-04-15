# Fossil Occurrence Prediction in the Morrison Formation (Colorado) Using Geospatial Machine Learning

## Project Overview

This project investigates whether fossil occurrence within a **[subset of the Morrison Formation in Colorado](https://www.researchgate.net/figure/Map-showing-the-geographic-distribution-of-the-Morrison-Formation-across-the-Western_fig2_223834682)** can be predicted using geological and topographic features. The Morrison Formation (Late Jurassic) is one of the most fossil-rich sedimentary units in North America, particularly known for abundant dinosaur and vertebrate fossils.

Rather than modeling across the entire Western U.S., this project focuses on a **geographically constrained, fossil-rich region in central and western Colorado**, allowing for a controlled and interpretable machine learning setup.

By framing fossil discovery as a **spatial machine learning problem within a single formation and region**, this project aims to learn fine-scale patterns that distinguish fossil-bearing locations from non-productive areas, avoiding trivial predictions driven by large-scale geological differences.

---

## Research Question

Within a defined subset of the Morrison Formation in Colorado, can fossil occurrences be predicted using geological and terrain-based features?

---

## Hypothesis

Fossil presence within the Morrison Formation is influenced by:

- **Depositional environment variation** (e.g., fluvial channels vs floodplains)
- **Lithological differences** within the formation
- **Surface exposure conditions** driven by terrain (e.g., slope, erosion, outcrop visibility)

A machine learning model using these features should outperform naive baselines in identifying fossil-bearing locations within the region.

---

## Scope

### Constraints

To ensure feasibility, the project is intentionally scoped:

- **Single formation: Morrison Formation**
- **Single region: Central/Western Colorado subset**
- **Binary classification task (fossil vs no fossil)**
- **Limited feature set (geology + terrain only)**
- **No deep learning**
- **Sampling bias acknowledged but minimally addressed**

---

## Study Design

### Study Area

- Focus: **Central and Western Colorado**
- Restricted to:
  - Morrison Formation extent (primary)

This region is chosen because:
- High fossil density
- Relatively consistent geological structure
- Manageable spatial scale

---

### Key Design Choice (Important)

The model is trained **within a geographically constrained subset of the Morrison Formation**, not across the full Western U.S. Avoids region--Morrison--as a feature label as this could cause the model to see Morrison and report positive for fossil presence.

This ensures the model learns:

> intra-formation variation (e.g., exposure, lithology differences)

instead of trivial rules like:

> “if formation = Morrison \exists fossil”

---

### Spatial Representation

- Region divided into a grid:
  - **1 km × 1 km cells**
- Each cell represents one data point with:
  - Feature vector (geology + terrain)
  - Binary label (fossil presence)

---

### Labels

Binary classification:

- `1` → Cell contains ≥1 fossil occurrence (from PBDB)
- `0` → No recorded fossil occurrence

Important considerations:
- Negative labels represent **absence of recorded fossils**, not confirmed absence
- Introduces label noise and sampling bias

---

## Data Sources

### Fossil Occurrence Data

- [Paleobiology Database](https://paleobiodb.org/#/) (PBDB)

Filter criteria:
- Geographic bounding box: Central/Western Colorado
- Optional: restrict to vertebrate or dinosaur fossils
- Optional: filter by Morrison Formation where available

Fields used:
- Latitude / Longitude
- Taxonomy
- Geological age

---

### Geological Data

- [Macrostrat](https://macrostrat.org/) or [USGS](https://ngmdb.usgs.gov/ngm-bin/ngm_compsearch.pl)

Features extracted:
- Lithology (categorical)
- Sedimentary vs non-sedimentary indicator
- Geological age
- Sub-formation unit (if available)

---

### Terrain Data

- [USGS](https://topobuilder.nationalmap.gov/) (DEM)

Derived features:
- Elevation
- Slope
- Aspect (encoded as sin/cos)
- Terrain ruggedness / local relief

---

## Feature Set

### Geological Features
- Lithology (one-hot encoded)
- Sedimentary indicator (binary)
- Geological age (numeric or binned)
- Sub-formation unit (if available)

### Terrain Features
- Elevation
- Slope
- Aspect (sin/cos encoding)
- Terrain ruggedness

Total: **~6–10 features**

---

## Handling Class Imbalance

### Training Strategy

- If there is an inbalance of positive and "negative" cells in the data gathered, tighten dataset to be of even counts of positives and "negatives".

This prevents trivial solutions from class imbalance`.

### Important

- **Training set is balanced**
- **Test set remains imbalanced**

---

## Models

### Baseline
- Logistic Regression

### Tree-Based Models
- Random Forest
- XGBoost (primary model)

Rationale:
- Strong performance on tabular geospatial data
- Captures nonlinear relationships
- Interpretable via feature importance

---

## Evaluation Strategy

### Train/Test Split

- Use **spatial splitting**
- Divide region into geographic blocks:
  - Train on subset of blocks
  - Test on held-out blocks

Prevents spatial leakage and overfitting.

---

### Metrics

- ROC-AUC
- Precision / Recall
- PR-AUC (important for imbalance)

### Key Metric

**Top-K Recall**
- Evaluate how many fossil cells fall within top X% of predicted locations

Interpretation:
> If we search the top predicted areas, how many fossils do we recover?

---

## Expected Outputs

- Clean geospatial dataset (grid-based)
- Trained models
- Feature importance analysis
- Heatmap of predicted fossil likelihood
- Quantitative evaluation metrics

---

## Limitations

- Fossil data is **presence-only**
- Negative labels are noisy (sampling bias)
- Fossil discovery depends on human sampling patterns
- Geological maps may vary in resolution
- Model generalization beyond this region is not guaranteed

---

## Timeline (12 Weeks)

### Month 1: Data Construction
- Define Colorado bounding box
- Download PBDB fossil data
- Obtain geological maps
- Process DEM data
- Construct grid and labels
- Build dataset

---

### Month 2: Modeling
- Exploratory data analysis
- Train baseline models
- Train Random Forest / XGBoost
- Generate predictions
- Create initial maps

---

### Month 3: Refinement & Write-Up
- Implement spatial splitting
- Feature ablation
- Improve visualizations
- Write report/paper

---

## Final Deliverable

A research-style report demonstrating:

- A reproducible geospatial ML pipeline
- Evidence that fossil occurrence within the Morrison Formation (Colorado) is predictable
- A ranked map of high-probability fossil locations
- Clear discussion of limitations and sampling bias