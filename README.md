# 🧬 Molecular-Skeleton-Programming

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](https://opensource.org/licenses/MIT)

This repository provides a notebook workflow for molecular skeleton programming tasks. The current example focuses on dichloropyrimidine and includes molecular combination generation, symmetry-based duplicate removal, sampling, feature selection, model tuning, stacking evaluation, SHAP-based interpretation, and prediction on descriptor tables.

The current project uses two targets, `Gap` and `Barrier`. These targets are handled by running the feature engineering and modeling steps separately.

## 🚀 Main Components

* **Molecular library generation 🧱**: Enumerates functional-group substitutions on the molecular scaffold and removes symmetry-equivalent structures.
* **Sampling 🧪**: Selects a representative subset from the generated molecular library for later calculations.
* **Feature engineering 📌**: Uses Pearson correlation filtering, SHAP-based coarse selection, and iterative feature selection.
* **Model tuning ⚙️**: Tunes several regression models with Bayesian optimization.
* **Stacking evaluation 📊**: Builds a stacking model from selected base learners and evaluates it with cross-validation.
* **Prediction 🔮**: Applies the trained workflow to new descriptor tables.

---

## 📁 Repository Structure

```text
.
├── 🧱 Molecule Generation & Sampling.ipynb      # Script 1: molecular generation and sampling
├── 📌 Feature Engineering.ipynb                 # Script 2: feature engineering
├── 📊 Tree_stacking.ipynb                       # Scripts 3-5: tuning, stacking, evaluation, prediction
├── 📄 Original_data.xlsx                        # Descriptor table used in this example
├── 💻 requirements.txt                          # Python dependencies
├── 📜 LICENSE                                   # MIT License
└── 📘 README.md                                 # This document
```

---

## 🧭 Workflow Overview

**Script 1: Molecule Generation & Sampling**

Defines the substitution sites, generates molecular combinations, removes duplicates, and exports sampled molecules.

⬇️

**Script 2: Feature Engineering**

Reads a descriptor table, selects features, and exports a target-specific training table.

⬇️

**Script 3: Hyperparameter Optimization**

Tunes the enabled base regression models.

⬇️

**Script 4: Stacking Ensemble & Evaluation**

Builds the stacking model, evaluates it, and exports prediction and SHAP files.

⬇️

**Script 5: Prediction**

Uses the trained model workflow to predict new samples.

⚠️ Scripts 3, 4, and 5 are all inside `Tree_stacking.ipynb`. Run them in the same notebook session so variables such as `grid_searches` and `global_best_meta_learner` remain available.

---

## 📘 Script 1: Molecule Combination Generation & Sampling

### 🎯 Function

* Generates all possible molecular combinations from substitutable sites and functional groups.
* Removes duplicate structures according to the site symmetry definition.
* Samples molecules from the generated library.
* Displays a UMAP plot for the generated and sampled molecules.

### ▶️ Usage

1. Open `Molecule Generation & Sampling.ipynb`.
2. Edit the `User Configuration` section.
3. Run the notebook cells.

### ⚙️ Main Parameters

* `NUM_SITES`: Number of substitutable sites. The current value is `3`.
* `NUM_FUNCTIONAL_GROUPS`: Number of functional groups. The current value is `7`.
* `SYMMETRY_STRING`: Site symmetry definition. The current value is `"1,3"`.
* `SAMPLE_FRACTION`: Fraction of molecules to sample. The current value is `0.2`.
* `RANDOM_SEED`: Random seed for sampling and UMAP. The current value is `0`.

### 📄 Outputs

* `all_possible_molecules.xlsx`: All unique molecular combinations.
* `representative_sample.xlsx`: Sampled molecular combinations.
* UMAP plot window: Distribution of sampled molecules within the generated library.

### 🧾 Output Format

The generated molecule is represented by a digit string, such as `"216"`.

* The digit position corresponds to the substitution site.
* The digit value corresponds to the functional group.
* For this dichloropyrimidine task, `1` to `7` correspond to `-H`, `-Me`, `-Et`, `-NH2`, `-OCH3`, `-CF3`, and `-CN`.
* For other molecular skeletons, site definitions, symmetry rules, functional-group mapping, and sampling parameters should be set according to the structural characteristics of the target skeleton.
* Example: `"216"` means site 1 is `-Me`, site 2 is `-H`, and site 3 is `-CF3`.

---

## 📘 Script 2: Feature Engineering

### 🎯 Function

* Reads a descriptor table from Excel.
* Extracts feature columns and one target column.
* Removes highly correlated features by Pearson correlation.
* Performs SHAP-based coarse feature selection with Random Forest.
* Performs iterative feature selection with cross-validation.

### ▶️ Usage

1. Open `Feature Engineering.ipynb`.
2. Set the input table through `INPUT_FILE`.
3. Set the feature and target columns through `FEATURE_COLUMN_SLICE` and `TARGET_COLUMN_INDEX`.
4. Run the notebook once for `Gap` and once for `Barrier`.

### ⚙️ Main Parameters

* `INPUT_FILE`: Input descriptor table. The script default is `'your_data.xlsx'`. The example in this document is `Original_data.xlsx`.
* `OUTPUT_DIR`: Output folder. The current value is `'.'`.
* `FEATURE_COLUMN_SLICE`: Feature-column slice. The current value is `'1:-2'`.
* `TARGET_COLUMN_INDEX`: Target-column index. Use `-2` for `Gap` and `-1` for `Barrier` in the example dual-target table.
* `PEARSON_CORR_THRESHOLD`: Pearson correlation threshold. The current value is `0.80`.
* `SHAP_COARSE_SELECTION_PERCENT`: Fraction of features kept after SHAP coarse selection. The current value is `0.8`.
* `EARLY_STOPPING_PATIENCE`: Early stopping patience for iterative selection. The current value is `5`.
* `PERFORMANCE_METRIC`: Metric for iterative selection. The current value is `'r2'`; `'mae'` is also supported.
* `KFOLD_SPLITS`: Number of folds for cross-validation. The current value is `10`.

### 📄 Outputs

The output names use the target column name as `{target}`.

* `selected_features_{target}.xlsx`: Selected feature table for model training.
* `feature_engineering_summary_{target}.xlsx`: Feature-count summary for each stage.
* `feature_engineering_iteration_history_{target}.xlsx`: Iterative selection history.
* `feature_correlation_original_{target}.xlsx`: Original feature correlation matrix.
* `feature_correlation_stage1_{target}.xlsx`: Correlation matrix after Pearson filtering.
* `feature_correlation_final_{target}.xlsx`: Correlation matrix for final selected features.

---

## 📘 Script 3: Hyperparameter Optimization

### 🎯 Function

* Reads the selected feature table from Script 2.
* Tunes the enabled base regression models.
* Stores the tuning results in memory for Script 4.

### ▶️ Usage

Run the hyperparameter optimization section in `Tree_stacking.ipynb`.

For the two targets, use one of these inputs:

* `EXCEL_FILE_PATH = 'selected_features_gap.xlsx'`
* `EXCEL_FILE_PATH = 'selected_features_barrier.xlsx'`

### ⚙️ Main Parameters

* `EXCEL_FILE_PATH`: Input training table.
* `X_COLS_SLICE`: Feature-column slice. The current value is `slice(1, -1)`.
* `Y_COLS_SLICE`: Target-column slice. The current value is `-1`.
* `CV_N_SPLITS`: Number of folds for model tuning. The current value is `10`.
* `N_ITER_BAYESIAN`: Number of Bayesian optimization iterations. The current value is `50`.
* `ENABLED_MODELS`: Enabled base models. Current options include `XGBR`, `RF`, `GBRT`, `HGBR`, `ETR`, `CBR`, and `LGBM`.

### 📄 Outputs

* Console logs for model tuning.
* `grid_searches`: In-memory dictionary containing fitted search objects and estimators for the enabled models.

---

## 📘 Script 4: Stacking Ensemble & Evaluation

### 🎯 Function

* Uses `grid_searches` from Script 3.
* Generates out-of-fold predictions for base learners.
* Tunes an ElasticNet meta-learner.
* Evaluates base learners and the stacking model across repeated seeds.
* Exports cross-validation predictions, scores, and SHAP-related files.

### ▶️ Usage

Run this section directly after Script 3 in the same `Tree_stacking.ipynb` session.

### ⚙️ Main Parameters

* `ENABLED_BASE_LEARNERS`: Base learners used in stacking. Keep this consistent with `ENABLED_MODELS`.
* `N_SEEDS_FOR_EVALUATION`: Number of evaluation seeds. The current value is `100`.
* `N_SPLITS_OUTER_CV`: Number of outer CV folds. The current value is `10`.
* `META_LEARNER_N_ITER_BAYESIAN`: Number of Bayesian optimization iterations for the meta-learner. The current value is `50`.
* `META_LEARNER_N_SPLITS_CV`: Number of internal CV splits for the meta-learner. The current value is `10`.
* `META_LEARNER_N_REPEATS_CV`: Number of internal CV repeats for the meta-learner. The current value is `3`.
* `N_FEATURES_TO_PLOT`: Number of features shown in SHAP plots. The current value is `30`.
* `PLOT_SHAP_SWARM_PLOT`: Whether to export the SHAP swarm plot. The current value is `True`.

### 📄 Outputs

* `cv_predictions_{target}.xlsx`: Cross-validation prediction table.
* `cv_model_scores_{target}.xlsx`: R² and RMSE scores by seed and fold.
* `unified_feature_importances_{target}.png`: Feature-importance bar chart from the two-level weighted SHAP calculation.
* `shap_summary_plot_{target}.png`: SHAP swarm plot.
* `shap_swarm_data_{target}.xlsx`: Data used for the SHAP swarm plot.

---

## 📘 Script 5: Prediction

### 🎯 Function

* Uses the trained stacking workflow to generate predictions.
* Exports prediction results to Excel or CSV.

### ▶️ Usage

Run this section after Script 4 in the same `Tree_stacking.ipynb` session.

### ⚙️ Main Parameters

* `UNKNOWN_DATA_FILE`: Excel file used for prediction.
* `UNKNOWN_DATA_FILE_COLUMN_RANGE`: Feature range in the prediction file. The current value is `(slice(None), slice(1, None))`.
* `REUSE_PRETRAINED_STACKING_MODEL`: Whether to reuse the model trained in Script 4. The current value is `True`.
* `ENABLED_BASE_LEARNERS`: Base learners used for prediction. Keep this consistent with Script 4.
* `PREDICT_CV_N_SPLITS`: CV folds used if retraining is enabled. The current value is `10`.
* `PREDICT_META_LEARNER_N_SPLITS_CV`: Internal CV splits for the prediction-stage meta-learner. The current value is `10`.
* `PREDICT_META_LEARNER_N_REPEATS_CV`: Internal CV repeats for the prediction-stage meta-learner. The current value is `3`.
* `PREDICTION_OUTPUT_FILENAME_PREFIX`: Output prefix. The current value is `'prediction_results'`.
* `PREDICTION_EXPORT_TO_EXCEL`: Whether to export Excel output. The current value is `True`.
* `PREDICTION_EXPORT_TO_CSV`: Whether to export CSV output. The current value is `False`.

### 📄 Outputs

* `prediction_results_{target}.xlsx`: Prediction results when Excel export is enabled.
* `prediction_results_{target}.csv`: Prediction results when CSV export is enabled or Excel export fails.

⚠️ The prediction feature columns must match the training feature columns in number and order.

---

## 💻 How to Use

1. Create and activate a Python environment.

```bash
python -m venv venv
```

Windows PowerShell:

```bash
.\venv\Scripts\Activate.ps1
```

Windows CMD:

```bash
venv\Scripts\activate
```

Linux/macOS:

```bash
source venv/bin/activate
```

2. Install dependencies with `requirements.txt`.

```bash
pip install -r requirements.txt
```

3. Open the notebooks in Jupyter or JupyterLab and run the cells sequentially.

4. Run `Molecule Generation & Sampling.ipynb` to generate `all_possible_molecules.xlsx` and `representative_sample.xlsx`.

5. Prepare a descriptor table. The example in this document is `Original_data.xlsx`.

6. Run `Feature Engineering.ipynb` separately for `Gap` and `Barrier`.

7. Run `Tree_stacking.ipynb` separately for `Gap` and `Barrier`.

---

## 🧩 Environment Notes

This project was developed with Python `3.10.18`. Other Python 3.10+ environments may work, but small numerical differences can occur.

Core dependencies are listed in `requirements.txt`, including:

```text
pandas==2.2.2
numpy==1.26.4
matplotlib==3.8.4
seaborn==0.13.2
scikit-learn==1.6.1
xgboost==3.0.2
catboost==1.2.7
lightgbm==4.6.0
scikit-optimize==0.10.2
shap==0.48.0
umap-learn==0.5.7
tqdm==4.67.1
openpyxl==3.1.5
jupyterlab>=4.0.0
notebook==7.3.2
ipykernel==6.29.5
ipywidgets==8.1.7
```

Small differences in numerical output may come from:

* Different sub-dependency versions selected during installation.
* Different compiled math libraries across operating systems or hardware.
* Bayesian optimization paths affected by small floating-point differences.
* Parallel computation changing the order of some floating-point operations.

---

## 📜 License and Correspondence

The data and code in this repository are available under the [MIT License](LICENSE).

For inquiries, please correspond with Prof. [Guangmin Zhou](mailto:guangminzhou@sz.tsinghua.edu.cn) 📟.

---

## 🙏 Acknowledgements

[Yifei Zhu](mailto:zhuyifeiedu@126.com) at Tsinghua University conceived and formulated the algorithms, built the quantum chemistry database, developed and deposited the code, and authored this guideline document.
