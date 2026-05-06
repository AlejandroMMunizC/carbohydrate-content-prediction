# Data

The raw **CGMacros** dataset is **not stored in this repository** because:

1. It is large (~3.6 GB uncompressed, ~660 MB zipped) and exceeds GitHub's recommended limits.
2. It is publicly available from PhysioNet, governed by its own license, and should be obtained from the original source.

## How to download the dataset

1. Go to the dataset's PhysioNet page:
   **https://physionet.org/content/cgmacros/1.0.0/**

2. Download `cgmacros-a-scientific-dataset-for-personalized-nutrition-and-diet-monitoring-1.0.0.zip` (or use the `wget` command provided on PhysioNet).

3. Unzip it into `data/raw/` so the layout looks like:

   ```
   data/
   ├── README.md                                ← this file
   ├── processed/                               ← shipped with the repo
   └── raw/
       └── cgmacros-a-scientific-dataset-for-personalized-nutrition-and-diet-monitoring-1.0.0/
           ├── CGMacros_dateshifted365/
           │   ├── CGMacros/                    ← per-subject CGM CSVs
           │   ├── CGMacros_Dataset/
           │   ├── meals.csv
           │   ├── bio.csv
           │   ├── microbes.csv
           │   ├── gut_scores.csv
           │   └── ...
           ├── DataDictionaries/
           ├── LICENSE.txt
           └── SHA256SUMS.txt
   ```

4. The notebook in `notebooks/carbohydrate_prediction.ipynb` contains the file-loading paths near the top — adjust them if you place the data somewhere else.

## What is shipped here

The `processed/` folder contains a few small CSVs that are **outputs** of the notebook, useful as a quick reference without having to rerun the whole pipeline:

| File | Description |
|------|-------------|
| `meals_macros.csv` | Mean macronutrient breakdown by meal type (breakfast, lunch, dinner, snack) |
| `macronutrient_summary.csv` | Descriptive statistics (count, mean, std, min, quartiles, max) for the 1,706 cleaned meal events |
| `cluster_profiles.csv` | Centroid characteristics for each glucose-response cluster identified via KMeans |

## Citation

If you use the CGMacros dataset, please cite:

> Bent, B., Hernandez, B., Wang, Y., Romine, M., Dunn, J., Halamka, J. (2024). *CGMacros: A scientific dataset for personalized nutrition and diet monitoring* (version 1.0.0). PhysioNet. https://doi.org/10.13026/cgmacros-1.0.0
