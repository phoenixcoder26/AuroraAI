# AuroraAI: Aurora Borealis Forecasting Using Space Weather and Machine Learning

**AuroraAI** is a machine learning project that predicts aurora-likely space-weather conditions using NASA OMNIWeb solar wind and geomagnetic data. The project combines leakage-aware supervised learning, threshold tuning, model evaluation, and planned unsupervised space-weather regime discovery.

The goal is to build a scientifically grounded aurora forecasting workflow that can later be extended into an interactive tracker dashboard.

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

**NASA OMNIWeb**

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

Threshold tuning improved aurora recall from **41.7% to 58.3%** and reduced missed aurora-likely events from **56 to 40**.

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

## Planned Dashboard

A final portfolio dashboard will combine:

* Supervised aurora prediction results
* Threshold tuning explanation
* Kp index educational explainer
* Interactive aurora visibility globe
* Unsupervised space-weather regime clusters
* Model interpretation and visual storytelling

The dashboard will use a clean light aurora-inspired design.

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
* HTML/CSS/JavaScript for dashboard prototypes

---

## Repository Structure

```text
AuroraAI/
│
├── data/
│   ├── raw/
│   └── processed/
│
├── notebooks/
│   └── AuroraAI_Modeling.ipynb
│
├── outputs/
│   ├── 01_roc_curve.png
│   ├── 02_precision_recall_curve.png
│   ├── 03_threshold_vs_metrics.png
│   ├── 04_fp_vs_fn.png
│   ├── 05_confusion_matrix_comparison.png
│   ├── 06_feature_importance.png
│   ├── 07_before_after_comparison.png
│   └── 08_full_dashboard.png
│
├── website_assets/
│   ├── aurora_light_advanced_dashboard.html
│   └── kp_aurora_globe_interactive.html
│
├── README.md
├── requirements.txt
└── .gitignore
```

---

## Project Status

Current status:

```text
Supervised learning: completed
Threshold tuning: completed
Advanced visual prototypes: in progress
Unsupervised learning: next stage
Final dashboard website: planned
```

---

## Author

**Farzana Khan Moutushi**
M.S. Applied Analytics, Columbia University
Machine Learning | Financial Data Science | AI & Analytics Portfolio
