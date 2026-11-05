# AuroraAI: Aurora Borealis Forecasting Using Space Weather and Machine Learning

**AuroraAI** is a machine learning project that predicts aurora-likely space-weather conditions using NASA OMNIWeb solar wind and geomagnetic data. The project combines leakage-aware supervised learning, threshold tuning, model evaluation, and planned unsupervised space-weather regime discovery.

The goal is to build a scientifically grounded aurora forecasting workflow, presented as an interactive website.

**Live site:** https://phoenixcoder26.github.io/AuroraAI/ (rotating aurora globe, storm-season replay, threshold slider, ROC and precision-recall curves)

---

## Project Overview

Aurora activity is strongly influenced by geomagnetic disturbances caused by solar wind and interplanetary magnetic field behavior. In this project, I used NASA OMNIWeb hourly space-weather data to classify whether conditions are aurora-likely.

The target variable was defined using the Kp index:

```python
aurora_visible = 1 if Kp >= 5 else 0
```

A Kp value of 5 or higher indicates geomagnetic storm-level activity, where aurora becomes more likely and may be visible beyond high-latitude regions.

---

## Key Objective

The main objective of AuroraAI is to answer:

> Can solar wind, magnetic field, and geomagnetic indicators be used to predict aurora-likely conditions without directly leaking the target signal?

To reduce leakage-like behavior, the final supervised model excludes:

* `ae_nt`
* `kp`
* `kp_x10`
* `aurora_visible`

The model uses solar wind, magnetic field, Dst index, electric field, pressure, and time-series lag/rolling features as predictors.

---

## Data Source

The project uses hourly space-weather data from:

**NASA OMNIWeb**, 8,760 hourly rows for 2025 (8,435 hours after removing missing values and building lag features). Storm hours (Kp >= 5) are 5.5% of the data.

The raw file is included in this repository at `data/omni2__LqhfgxZ4u.lst`.

Main feature categories include:

* Interplanetary Magnetic Field magnitude
* IMF Bz and By GSM components
* Solar wind speed
* Proton density
* Proton temperature
* Flow pressure
* Electric field
* Dst geomagnetic index
* Lag and rolling time-series features

---

## Methodology

The project includes the following stages:

1. Data loading and cleaning
2. Missing value handling using NASA OMNIWeb sentinel values
3. Feature engineering with lag and rolling-window variables
4. Leakage-aware supervised classification
5. Time-aware train-test split
6. Random Forest modeling
7. Threshold tuning
8. ROC, precision-recall, F1, and confusion matrix evaluation
9. Feature importance analysis
10. Planned unsupervised clustering for space-weather regime discovery

---

## Supervised Learning Model

The final supervised model is a leakage-aware Random Forest classifier.

### Model

```text
Random Forest Classifier
Class weighting: balanced
Target: aurora_visible
Positive class: Kp >= 5
```

### Model Results

| Metric    | Baseline Threshold 0.50 | Tuned Threshold 0.40 |
| --------- | ----------------------: | -------------------: |
| Accuracy  |                   0.962 |                0.967 |
| Precision |                   0.833 |                0.778 |
| Recall    |                   0.417 |                0.583 |
| F1-score  |                   0.556 |                0.667 |
| ROC-AUC   |                  0.9769 |               0.9769 |
| PR-AUC    |                   0.766 |                0.766 |

Threshold tuning improved aurora recall from **41.7% to 58.3%** and reduced missed aurora-likely hours from **56 to 40**.

**Test setup:** the split is time-aware. The first 80% of hours trained the model and the last 20% (Oct 12 to Dec 21, 2025; 1,687 hours, 96 of them storm hours) tested it. A model with no skill would score a PR-AUC of 0.057 (the storm-hour share of the test set), so 0.766 is well above chance.

**Storm-event view:** the test period contains 14 separate storm events (runs of consecutive Kp >= 5 hours). At threshold 0.40 the model flagged at least one hour in **10 of 14** events (9 of 14 at 0.50).

---

## Confusion Matrix Comparison

### Baseline Threshold 0.50

|                  | Predicted No Aurora | Predicted Aurora |
| ---------------- | ------------------: | ---------------: |
| Actual No Aurora |                1583 |                8 |
| Actual Aurora    |                  56 |               40 |

### Tuned Threshold 0.40

|                  | Predicted No Aurora | Predicted Aurora |
| ---------------- | ------------------: | ---------------: |
| Actual No Aurora |                1575 |               16 |
| Actual Aurora    |                  40 |               56 |

The tuned threshold increased true aurora detections from **40 to 56**, while false alerts increased moderately from **8 to 16**. For an aurora tracker, this is a useful tradeoff because missed aurora events are more costly than a small number of additional false alerts.

---

## Feature Importance

The most important predictors included:

* Dst geomagnetic index
* IMF magnitude
* Solar wind pressure
* Rolling Bz values
* Electric field
* Proton temperature
* Solar wind speed
* Lagged Bz features

These features align with the physical intuition that aurora activity is driven by geomagnetic disturbance, magnetic field orientation, solar wind dynamics, and sustained changes in space-weather conditions.

---

## Limitations

* One year of data and one test window, so results may not hold in other solar-cycle conditions.
* Kp measures geomagnetic activity, not whether aurora is visible from a given city (weather, darkness and local time also matter).
* Dst is the model's most important feature and is a measured geomagnetic index closely related to Kp. A planned check is to retrain without it to see how much of the skill remains.
* The aurora oval, city visibility and the Bz / wind-speed sandbox on the website are simplified teaching visuals, labeled as such. The charts and numbers come from the real test predictions.

---

## Unsupervised Learning: Next Stage

The next stage of AuroraAI focuses on unsupervised learning to discover natural space-weather regimes.

Planned methods include:

* K-Means clustering
* PCA visualization
* Silhouette score analysis
* Cluster profile heatmaps
* Aurora rate comparison by cluster
* Space-weather regime interpretation

This stage will help answer:

> Can natural clusters of space-weather behavior reveal calm, moderate, and storm-like aurora regimes?

---

## Interactive Website

The site at https://phoenixcoder26.github.io/AuroraAI/ presents the results with real test-set predictions:

* An aurora globe whose oval moves with the Kp index
* A replay of all 1,687 test hours against the 14 real storm events
* One alarm-threshold slider that updates the confusion matrix, precision, recall, F1, and the ROC and precision-recall curves
* Feature importance, by feature and by family
* A clearly labeled illustrative sandbox for Bz and solar-wind speed

The site data is stored in `data/aurora_data.json`, exported from the notebook's final cell.

---

## Tools and Libraries

* Python
* Pandas
* NumPy
* Scikit-learn
* Matplotlib
* Seaborn
* Plotly
* Jupyter Notebook
* HTML/CSS/JavaScript (canvas and SVG, no libraries) for the website

---

## Repository Structure

```text
AuroraAI/
│
├── data/
│   ├── omni2__LqhfgxZ4u.lst      # NASA OMNIWeb hourly data, 2025
│   └── aurora_data.json          # real test-set results used by the website
│
├── notebooks/
│   └── Aurora_Modeling.ipynb     # cleaning, features, model, threshold tuning, export
│
├── docs/
│   └── index.html                # the website (GitHub Pages)
│
├── README.md
└── requirements.txt
```

---

## Run It Yourself

```bash
pip install -r requirements.txt
jupyter notebook notebooks/Aurora_Modeling.ipynb
```

In the notebook, set `project_path` (cell 3) to the folder that contains the data file, for example the `data` folder in this repository. Run all cells; the last cell regenerates `aurora_data.json`.

---

## Project Status

Current status:

```text
Supervised learning: completed
Threshold tuning: completed
Interactive website: published
Unsupervised learning: next stage
```

---

## Author

**Farzana Khan Moutushi**
