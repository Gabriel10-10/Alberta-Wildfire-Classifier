# Alberta Wildfire Classifier

Machine learning classification of final wildfire size classes (A–E) using historical Alberta wildfire records and information available at initial assessment.

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![scikit--learn](https://img.shields.io/badge/ML-scikit--learn-orange)
![Development status](https://img.shields.io/badge/status-in%20development-yellow)

## Overview

The Alberta Wildfire Classifier analyzes historical wildfire records from Alberta, Canada (2006–2025) and develops supervised classification models to predict `SIZE_CLASS` from incident characteristics recorded at or before `ASSESSMENT_DATETIME`.

The project covers exploratory data analysis, data validation, feature engineering, model development, performance evaluation, and application deployment. Predictions are research-oriented estimates and are **not intended for operational wildfire response or safety-critical decisions**.

## Dataset

| Property | Value |
| --- | --- |
| Publisher | Government of Alberta |
| Coverage | 2006–2025 |
| Observations | 27,828 in the source CSV used for initial analysis |
| Target | `SIZE_CLASS` (A, B, C, D, E) |
| Prediction point | Initial assessment (`ASSESSMENT_DATETIME`) |
| Raw dataset | `data/raw/fp-historical-wildfire-data-2006-2025.csv` |

**Sources**

- [Historical wildfire data — Government of Canada Open Data Portal](https://open.canada.ca/data/en/dataset/a221e7a0-4f46-4be7-9c5a-e29de9a3447e)
- [Historical wildfire data dictionary, 2006–2025](https://open.alberta.ca/dataset/a221e7a0-4f46-4be7-9c5a-e29de9a3447e/resource/1b635b8b-a937-4be4-857e-8aeef77365d2/download/fp-historical-wildfire-data-dictionary-2006-2025.pdf)
- [Open Government Licence – Alberta](https://open.alberta.ca/licence)

The repository also contains a 2006–2018 data dictionary. The 2006–2025 dictionary is the preferred reference for the current dataset.

## Repository structure

```text
Alberta-Wildfire-Classifier/
├── data/
│   └── raw/
│       ├── fp-historical-wildfire-data-2006-2025.csv
│       └── af-historic-wildfires-2006-2018-data-dictionary.pdf
├── notebooks/
│   ├── Data_preview.ipynb
│   ├── General_preparation.ipynb
│   ├── DT_Modeling.ipynb
│   └── KNN_Modeling.ipynb
└── README.md
```

### Notebooks

| Notebook | Contents |
| --- | --- |
| `Data_preview.ipynb` | Raw-data exploration, class distribution, missingness, quality checks, and feature review |
| `General_preparation.ipynb` | Feature selection, categorical missing-value handling, stratified data split, and training-set-fitted numeric imputation |
| `DT_Modeling.ipynb` | Decision Tree feature preparation and categorical encoding |
| `KNN_Modeling.ipynb` | KNN feature preparation, categorical encoding, and numerical standardization |

The notebook implementations are exploratory. Model training, evaluation, and the deployable application are under development.

## Setup

**Requirements:** Python 3.10+ and Git.

```bash
git clone https://github.com/Gabriel10-10/Alberta-Wildfire-Classifier.git
cd Alberta-Wildfire-Classifier
python -m venv .venv
```

Activate the environment:

**Windows (PowerShell)**

```powershell
.\.venv\Scripts\Activate.ps1
```

**macOS / Linux**

```bash
source .venv/bin/activate
```

Install dependencies and launch Jupyter:

```bash
python -m pip install --upgrade pip
python -m pip install pandas numpy matplotlib scikit-learn notebook
jupyter notebook
```

Open `notebooks/Data_preview.ipynb` to inspect the raw dataset. Its analyses do not modify the source CSV.

`General_preparation.ipynb` writes split files to `data/processed/`; this directory must exist before running the current notebook implementation.

## Machine learning

The target is multiclass wildfire size classification with the following models:

| Algorithm | Role | Preprocessing |
| --- | --- | --- |
| Decision Tree | Interpretable baseline | Numerical imputation and categorical encoding |
| K-Nearest Neighbors (KNN) | Distance-based baseline | Numerical imputation, categorical encoding, and scaling |
| Random Forest | Ensemble classifier | Numerical imputation and categorical encoding |

The modeling workflow uses a shared training, validation, and test partition. Statistical preprocessing is fitted on training data only, with fitted transformations reused for validation, test, and inference. Future training modules will bundle transformations and estimators in scikit-learn pipelines.

### Evaluation

Model selection and reporting will consider:

- Macro F1-score and accuracy
- Per-class precision and recall
- Confusion matrices
- Class imbalance and errors on less frequent fire size classes
- Generalization across historical time periods

No predictive performance results are reported until the classifiers have been trained and evaluated.

## Feature selection and data quality

The prediction point is **initial assessment**. Target leakage can occur if attributes recorded after that point are used as predictors. Final fire size, suppression milestones, and other post-assessment information are therefore excluded from modeling features.

The `FIRE_NUMBER` prefix is examined as a potential forest-area indicator; the complete incident identifier is not a suitable predictor. Time-of-observation and source definitions for candidate weather, fuel, and fire-spread attributes require verification.

Missing values, duplicate incident identifiers, and potentially invalid numeric measurements are reviewed in `Data_preview.ipynb`.

## Application and deployment

Planned application components:

- Historical wildfire exploration dashboard
- Prediction form for initial-assessment attributes
- Predicted size class and estimated class probabilities
- Model evaluation views
- Containerized deployment with Docker

## Limitations

- Historical records may contain incomplete, inconsistent, or subsequently revised information.
- Size classes are imbalanced; accuracy alone may obscure weak performance on rare classes.
- Reporting practices, wildfire conditions, and geographic patterns can change over time.
- Predicted size classes are uncertain and must not be used as operational firefighting recommendations.

## Data attribution

Contains information licensed under the [Open Government Licence – Alberta](https://open.alberta.ca/licence), sourced from the Government of Alberta's historical wildfire data. The Government of Alberta has not endorsed this project.
