# Predicting Meal Carbohydrate Content from Continuous Glucose Monitoring (CGM)

> Master's Thesis (TFM) — Master in Data Analytics & Big Data, **Universidad Francisco de Vitoria (UFV)**
> Author: **Alejandro Magdiel Muñiz Corona** · 2025

Can we infer what someone ate just by looking at their blood-glucose curve? This project explores that question using machine learning on the public **CGMacros** dataset, focusing on predicting **carbohydrate intake** from the postprandial glucose response (PPGR) measured by Continuous Glucose Monitors (CGMs).

> The full thesis (Spanish) is available at [`docs/TFM_thesis.pdf`](docs/TFM_thesis.pdf). The notebook is also written in Spanish.

---

## Objective & Hypothesis

**Objective.** Estimate the macronutrient composition of a meal — primarily carbohydrates, but also protein, fat and fiber — using only the postprandial glucose curve recorded by a CGM, supplemented with anthropometric, clinical and gut-microbiome features.

**Hypothesis.** The shape and characteristics of the postprandial glucose curve (incremental area under the curve, slope, peak, time-to-peak, variability) carry enough information to estimate the carbohydrate content of the meal that produced it.

## Dataset

This work uses the **CGMacros** dataset, published on PhysioNet:

> Gutierrez-Osuna, R. et al. *CGMacros: A scientific dataset for personalized nutrition and diet monitoring* (v1.0.0). PhysioNet. https://physionet.org/content/cgmacros/1.0.0/

The dataset includes, for 45 participants over ~10 days:

- Two simultaneous CGM streams (Abbott **Libre** and Dexcom **G6**)
- Photographed and labeled meal events with macronutrient composition
- Anthropometric and clinical variables (BMI, HbA1c, fasting glucose, lipid panel…)
- Gut-microbiome relative abundances and gut-health scores
- Activity data (heart rate, METs)

The raw data is **not redistributed** in this repository — see [`data/README.md`](data/README.md) for download instructions.

## Pipeline at a Glance

1. **Data ingestion & cleaning** — load CGMacros tables, handle missing values, harmonize timestamps.
2. **Microbiome dimensionality reduction** — PCA on relative abundances of bacterial taxa.
3. **EDA** — distributions of macronutrients, glucose dynamics by meal type, demographic stratification.
4. **PPGR feature extraction** — for each meal event, compute incremental AUC (iAUC), peak, time-to-peak, slope and post-meal variability over the following window for both Libre and Dexcom signals.
5. **Feature engineering** — merge meal labels with PPGR features, anthropometrics, clinical labs, microbiome PCs and gut-health scores.
6. **Modeling** — train tree-based regressors (`XGBoost`, `RandomForest`) on three feature sets: Libre-only, Dexcom-only, and both sensors combined.
7. **Clustering of glucose curves** — KMeans on PPGR shape features to derive an interpretable "response profile" categorical feature, then refit the regressor with this added signal.
8. **Interpretability** — SHAP values to inspect which features drive carbohydrate predictions.

## Headline Results

| Model | Feature set | RMSE (g carbs) | R² |
|-------|-------------|----------------|------|
| XGBoost Regressor (best) | Libre + Dexcom + clinical + microbiome | **~23.2** | **~0.29** |
| XGBoost Regressor | Libre only | higher | lower |
| XGBoost Regressor + cluster feature | Libre + Dexcom + cluster id | slight improvement | slight improvement |

An R² near 0.3 is consistent with prior literature on the inverse problem of inferring nutrition from glucose response, where biological noise and inter-individual variability set a hard ceiling. Adding the cluster-based response-profile feature offered a small but consistent improvement.

A few aggregate outputs from the notebook are included in [`data/processed/`](data/processed/):

- `meals_macros.csv` — average macronutrient breakdown per meal type
- `macronutrient_summary.csv` — distribution statistics across all 1,706 meal events
- `cluster_profiles.csv` — average meal/PPGR features for each glucose-response cluster

## Repository Structure

```
.
├── README.md                ← you are here
├── LICENSE                  ← MIT
├── requirements.txt         ← Python dependencies (tested on 3.11)
├── .gitignore
├── notebooks/
│   └── carbohydrate_prediction.ipynb   ← main project notebook (Spanish)
├── data/
│   ├── README.md            ← how to download the CGMacros dataset
│   └── processed/           ← small derived CSVs produced by the notebook
└── docs/
    └── TFM_thesis.pdf       ← full thesis (Spanish, ~2.8 MB)
```

## Reproducing the Results

1. **Clone the repo**
   ```bash
   git clone https://github.com/<your-username>/carbohydrate-content-prediction.git
   cd carbohydrate-content-prediction
   ```

2. **Set up a Python environment** (Python 3.11 recommended)
   ```bash
   python -m venv .venv
   # Windows: .venv\Scripts\activate    macOS/Linux: source .venv/bin/activate
   pip install -r requirements.txt
   ```

3. **Download the dataset** — follow [`data/README.md`](data/README.md). The notebook expects the files under `data/raw/`.

4. **Run the notebook**
   ```bash
   jupyter lab notebooks/carbohydrate_prediction.ipynb
   ```

## Limitations & Future Work

- The dataset is small (**45 participants, ~2,000 meal events**) and meal logging is self-reported, which introduces label noise.
- The same meal can yield very different glucose responses across individuals — personalization (per-subject models or mixed-effects models) is a natural next step.
- Sequence models (LSTM, Transformers) operating directly on the raw CGM trace, instead of hand-crafted features, may capture dynamics that engineered features miss.
- Other macronutrients (fat, protein, fiber) were modeled but are markedly harder to predict from glucose alone and would benefit from additional signals (e.g., meal images, accelerometry).

## Citation

If you find this work useful, please cite the thesis and the underlying dataset:

```
Muñiz Corona, A. M. (2025). Modelado de la composición de carbohidratos a partir
de la respuesta glucémica postprandial. Trabajo Fin de Máster, Máster en Data
Analytics & Big Data, Universidad Francisco de Vitoria.
```

```
Bent, B., Hernandez, B., Wang, Y., Romine, M., Dunn, J., Halamka, J. (2024).
CGMacros: A scientific dataset for personalized nutrition and diet monitoring
(version 1.0.0). PhysioNet. https://doi.org/10.13026/cgmacros-1.0.0
```

## License

The code in this repository is released under the [MIT License](LICENSE). The thesis PDF (`docs/TFM_thesis.pdf`) is © 2025 Alejandro Magdiel Muñiz Corona — all rights reserved; please do not redistribute without permission.

The CGMacros dataset is governed by its own [PhysioNet Credentialed Health Data License](https://physionet.org/content/cgmacros/1.0.0/) — please follow its terms when using it.

## Contact

**Alejandro Magdiel Muñiz Corona** · amagdielmc@gmail.com
