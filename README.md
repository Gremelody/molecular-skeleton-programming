# 🔬 Molecular-skeleton-design

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](https://opensource.org/licenses/MIT)
[![Status](https://img.shields.io/badge/status-active-brightgreen.svg)]()

This repository provides a complete, modular workflow designed to integrate quantum chemistry with modern machine learning techniques to accelerate research in materials science and computational chemistry. The pipeline covers the entire process from high-throughput virtual library construction and representative sampling, to sophisticated feature engineering, hyperparameter optimization, and finally to the construction, evaluation, interpretation, and application of a high-performance stacking ensemble model.

## 🚀 Key Features

* **End-to-End Pipeline** 🛣️: Offers a complete solution from problem definition to final prediction, covering all critical stages of a scientific research task.
* **Library Sampling** 🧬: Employs symmetry analysis and an innovative hybrid sampling strategy to efficiently select the most informative and representative subset from a vast combinatorial space, saving significant resources on subsequent DFT calculations.
* **Advanced Feature Engineering** 🛠️: Utilizes a three-stage "Filter-Embedded-Wrapper" strategy, combining Pearson correlation, SHAP feature importance, and iterative wrapper-based selection to precisely identify the optimal feature set.
* **Efficient Hyperparameter Optimization** ⚡: Leverages the Bayesian Optimization algorithm to intelligently and efficiently search for the best hyperparameter combinations, far surpassing traditional grid or random search methods.
* **Powerful Stacking Ensemble** 🧠: Integrates multiple high-performance base models (e.g., XGBoost, LightGBM) and trains a meta-learner to optimally combine their predictions, significantly boosting predictive accuracy and generalization.
* **Unique Model Interpretability** 📊: Features a novel **"Two-Level Weighted SHAP Analysis"** method, which provides a globally unified and highly reliable feature importance ranking by considering the performance of both base models and the final ensemble.
* **Reproducibility & Robustness** 🔁: Ensures complete reproducibility of results through global random seeds and assesses model stability and robustness via repeated cross-validation.

---

## 📂 Repository Structure and Workflow

This repository contains two core research projects, each utilizing the complete code workflow. The `CPyr/` directory holds the main body of work, focusing on the **2-chloropyrimidine** molecular system as additives in `Li-S batteries`, which is the primary subject of this README. The `1,4-Benzoquinone/` directory contains a secondary application of the same workflow to the **1,4-Benzoquinone** system as additives in `Li-CO2 batteries`, serving as another example to validate the universality of this machine learning workflow and molecular skeleton design paradigm.

```
.
├── CPyr/                                         # 🧪 Primary Project: 2-chloropyrimidine (Dual-Target: Gap, Barrier)
│   ├── Molecule Generation & Sampling.ipynb         # Contains script 1 for building the dataset
│   ├── Feature Engineering.ipynb                    # Contains script 2 for feature engineering
│   ├── Tree_stacking.ipynb                          # Contains script 3-5 for Hyperparameter Optimization, Stacking Ensemble & Evaluation and Prediction
│   ├── Original dataset.xlsx                          # -> Raw database for Script 2 (the orange-labeled samples are the original samples, and the others are samples generated after site exchange)
│   ├── Gap-final dataset.xlsx                         # -> Feature engineering output for Gap target
│   ├── Barrier-final dataset.xlsx                     # -> Feature engineering output for Barrier target
│   ├── Gap-prediction.xlsx                            # -> Prediction set for Gap target
│   └── Barrier-prediction.xlsx                        # -> Prediction set for Barrier target
│
├── 1,4-Benzoquinone/                             # 🧪 Secondary Project: 1,4-Benzoquinone (Single-Target: Eb)
│   ├── Molecule Generation & Sampling-BQ.ipynb      # Contains script 1 for building the dataset
│   ├── Feature Engineering-BQ.ipynb                 # Contains script 2 for feature engineering
│   ├── Tree_stacking-BQ.ipynb                       # Contains script 3-5 for Hyperparameter Optimization, Stacking Ensemble & Evaluation and Prediction
│   ├── Original dataset-BQ.xlsx                        # -> Raw database for Script 2 (the orange-labeled samples are the original samples, and the others are samples generated after site exchange)
│   ├── Eb-final dataset-BQ.xlsx                        # -> Feature engineering output
│   └── Eb-prediction-BQ.xlsx                           # -> Prediction set
│
├── environment.yml                               # 📦 Conda environment config for cross-platform setup
├── requirements.txt                              # 📦 Pip dependencies for basic/non-Conda setup
├── spec-file.txt                                 # 📦 Exact Conda config for highest-fidelity reproducibility (Win x64)
├── Feature set-functional groups.xlsx            # 🗂️ 50-dimensional feature data for 18 functional groups
├── LICENSE                                       # 📜 The MIT License file
└── README.md                                     # 📄 The document you are currently reading
```

### Workflow Details

#### 1. 2-chloropyrimidine Project (CPyr - Dual-Target Task)
This is a dual-target study requiring separate models for `Gap` and `Barrier`.
1.  **Feature Engineering (Script 2)**:
    * Use `Original dataset.xlsx` as input.
    * You need to run `Feature Engineering.ipynb` **twice**:
        * First run: Set the target variable to the `Gap` column to generate `Gap-final dataset.xlsx`.
        * Second run: Set the target variable to the `Barrier` column to generate `Barrier-final dataset.xlsx`.
2.  **Model Training & Prediction (Scripts 3, 4, 5)**:
    * You also need to run the entire `Tree_stacking.ipynb` workflow **twice**:
        * First run: Use `Gap-final dataset.xlsx` as input to train, evaluate, and finally predict with `Gap-prediction.xlsx`, creating the **Gap prediction model**.
        * Second run: Use `Barrier-final dataset.xlsx` as input to train, evaluate, and finally predict with `Barrier-prediction.xlsx`, creating the **Barrier prediction model**.

#### 2. 1,4-Benzoquinone Project (Single-Target Task)
This is a more straightforward, single-target (`Eb`) study. Script filenames include a `-BQ` suffix for distinction.
1.  **Feature Engineering (Script 2)**: Run `Feature Engineering-BQ.ipynb`, using `Original dataset-BQ.xlsx` as input to produce `Eb-final dataset-BQ.xlsx`.
2.  **Model Training & Prediction (Scripts 3, 4, 5)**: Run `Tree_stacking-BQ.ipynb`, using `Eb-final dataset-BQ.xlsx` as input and `Eb-prediction-BQ.xlsx` for the final prediction.

---

### 📜 Script 1: Molecule Combination Generation & Sampling

* **🎯 Function**:
    * Generates all possible molecular combinations based on user-defined substitutable sites and functional groups.
    * Automatically identifies and eliminates duplicate structures using symmetry rules, ensuring a unique molecular library.
    * Executes an advanced hybrid sampling strategy to select a manageable and highly representative subset for subsequent DFT calculations.
    * Provides UMAP dimensionality reduction for intuitive visualization of the sampling coverage and uniformity.

* **▶️ Usage**:
    1.  Configure the core molecular structure parameters in the `User Configuration` section.
    2.  Run the script to generate Excel files and a UMAP visualization plot.

* **⚙️ Tunable Parameters**:
    * `NUM_SITES`: **(Required)** The total number of substitutable sites on the molecular scaffold.
    * `NUM_FUNCTIONAL_GROUPS`: **(Required)** The total number of available functional groups or building blocks.
    * `SYMMETRY_STRING`: **(Key)** Defines site symmetry. The format is flexible, e.g., `"1,3"` means sites 1 and 3 are equivalent; `"1,4; 2,3"` means 1 is equivalent to 4, AND 2 is equivalent to 3. Leave empty for no symmetry.
    * `SAMPLE_FRACTION`: The proportion of the total library to sample (a value between 0.0 and 1.0), e.g., `0.1` for 10%.
    * `RANDOM_SEED`: A random seed to ensure the reproducibility of sampling and visualization.

* **📄 Outputs**:
    * **Console Output**: Prints configuration details, the total number of unique molecules generated, statistics for each molecular pattern, and sampling targets.
    * `all_possible_molecules.xlsx`: The complete database containing all unique molecular combinations.
    * `representative_sample.xlsx`: The representative subset of molecules selected by the hybrid sampling strategy.
    * **UMAP Visualization Plot**: An interactive plot window showing the distribution of sampled points (red) within the entire chemical space (gray).
    * **📝 Output File Format Explanation**:
        * The generated molecule is represented by a string of Arabic numerals (e.g., `"216"`).
        * The **position** of a numeral corresponds to the **site** on the scaffold (e.g., the first digit is for site 1, the second for site 2, etc.).
        * The **numeral itself** represents the functional group at that site. The mapping varies by project:
            * For the primary **2-chloropyrimidine** project (`CPyr/`):
                * `1` to `7` correspond to `-H`, `-Me`, `-Et`, `-NH2`, `-OCH3`, `-CF3`, `-CN`.
            * For the secondary **1,4-Benzoquinone** project:
                * `1` to `6` correspond to `-F`, `-Cl`, `-H`, `-Me`, `-OCH3`, `-NH2`.
        * **Example (2-chloropyrimidine)**: A molecule represented as `"216"` means Site 1 is substituted with `-Me`, Site 2 with `-H`, and Site 3 with `-CF3`.

* **💡 Highlights**:
    * **Canonicalization for Deduplication**: Accurately removes duplicate structures by calculating a molecule's "canonical form."
    * **Hybrid Sampling Strategy**: Strictly pursues component balance, achieving an optimal trade-off between diversity and representativeness.

---

### 📜 Script 2: Feature Engineering

* **🎯 Function**:
    * Selects the optimal feature subset from an initial database created by the user (containing DFT results and various descriptors).
    * **Stage 1 (Filter)**: Removes redundant features with high linear correlation using the Pearson correlation coefficient.
    * **Stage 2 (Embedded)**: Performs a coarse selection using a SHAP-based Random Forest model to quickly eliminate features with low predictive power.
    * **Stage 3 (Wrapper)**: Employs an iterative backward elimination strategy, removing the least important feature one by one while monitoring model performance with cross-validation to find the best feature combination.

* **▶️ Usage**:
    1.  Prepare an Excel file containing all raw features and the target variable.
    2.  Configure the input/output paths and algorithm parameters in the `Configuration Section`.
    3.  Run the script to generate a series of result files in the specified output directory.

* **⚙️ Tunable Parameters**:
    * `INPUT_FILE`: **(Required)** The filename of your raw data Excel file.
    * `OUTPUT_DIR`: **(Required)** The name of the folder where all output results will be saved.
    * `FEATURE_COLUMN_SLICE` / `TARGET_COLUMN_INDEX`: **(Key)** Defines the location of your features and target in the Excel file, e.g., `'1:-2'` means features are from the second column to the second-to-last.
    * `PEARSON_CORR_THRESHOLD`: The correlation threshold for Stage 1; a lower value results in more aggressive filtering.
    * `SHAP_COARSE_SELECTION_PERCENT`: The percentage of top features to retain after the SHAP coarse selection in Stage 2.
    * `EARLY_STOPPING_PATIENCE`: In Stage 3, stops the iteration if model performance does not improve for a specified number of consecutive steps, preventing overfitting.
    * `PERFORMANCE_METRIC`: The performance metric for Stage 3, choose between `'r2'` (higher is better) or `'mae'` (lower is better).

* **📄 Outputs**:
    * **Console Output**: Real-time logs for each stage, including lists of dropped features and changes in the feature count.
    * `feature_engineering_summary_...xlsx`: A summary report clearly showing the number of features removed at each stage.
    * `final_selected_dataset_...xlsx`: **(Core Output)** The data file containing the optimal feature set and the target variable.
    * `...correlation_matrix_...xlsx`: Feature correlation matrices from different stages for analysis.
    * `performance_iteration_history_...xlsx`: A detailed log from Stage 3, recording the model's performance after each feature removal.

* **💡 Highlights**:
    * **Three-Stage Selection**: A rigorous process that combines the advantages of filter, embedded, and wrapper methods for highly reliable results.
    * **SHAP-Driven**: The core selection process is driven by SHAP values, ensuring accuracy and interpretability.

---

### 📜 Script 3: Hyperparameter Optimization

* **🎯 Function**:
    * Uses the optimal dataset produced by the feature engineering script.
    * Performs efficient hyperparameter tuning for multiple mainstream machine learning models (e.g., XGBoost, Random Forest, LightGBM).
    * Utilizes the Bayesian Optimization algorithm to intelligently search for the hyperparameter combination that maximizes model performance (R² score).

* **▶️ Usage**:
    1.  Use the `final_selected_dataset_...xlsx` file from Script 2 as input.
    2.  Select the models to optimize and set relevant parameters in the `User Configuration` section.
    3.  Run the script. Results will be printed to the console, and the key variable `grid_searches` will be stored in memory for the next script.

* **⚙️ Tunable Parameters**:
    * `EXCEL_FILE_PATH`: **(Required)** The input file, which should be the output from Script 2.
    * `X_COLS_SLICE` / `Y_COLS_SLICE`: **(Required)** Adjust the slicing rules based on the input file's structure.
    * `ENABLED_MODELS`: **(Key)** Select the models to be optimized by commenting/uncommenting lines in this list.
    * `N_ITER_BAYESIAN`: The number of iterations for Bayesian Optimization. A higher value leads to a more thorough search but takes longer.
    * `parameter_XGBR` (and others): (Advanced) Dictionaries for fine-tuning the hyperparameter search space for specific models.

* **📄 Outputs**:
    * **Console Output**:
        * A real-time progress bar for each model's tuning process.
        * Upon completion for each model, a clear printout of the **best cross-validated R² score** and the corresponding **optimal hyperparameter combination**.
        * A final summary of results for all models.
    * **In-Memory Variable**: (Important) Creates a dictionary variable named `grid_searches`, which contains the optimization results and the best estimator instance for each model. This variable is directly used by Script 4.

* **💡 Highlights**:
    * **Bayesian Optimization**: More intelligent and faster to converge than traditional grid search, finding better hyperparameters with less computational cost.
    * **Highly Modular**: Easily enable or disable different models to compare their performance.

---

### 📜 Script 4: Stacking Ensemble & Evaluation

* **🎯 Function**:
    * Constructs a powerful Stacking Ensemble model using the optimized hyperparameters from the previous step.
    * Generates Out-of-Fold (OOF) predictions from base models via cross-validation and uses them as features to train a meta-learner.
    * Also uses Bayesian Optimization to find the best hyperparameters for the meta-learner.
    * Performs repeated cross-validation with multiple random seeds to comprehensively and fairly evaluate the performance and robustness of the final Stacking model and each base model.
    * Calculates and visualizes the **Two-Level Weighted SHAP feature importance** for a unified model interpretation.

* **▶️ Usage**:
    1.  This script should be run directly after Script 3 in the same session/environment to inherit the optimized model variables.
    2.  Configure the evaluation and plotting parameters in the `User Configuration` section.
    3.  Run the script to generate detailed performance reports, visualizations, and data files.

* **⚙️ Tunable Parameters**:
    * `ENABLED_BASE_LEARNERS`: **(Key)** Selects the base models to be included in the Stacking ensemble. **MUST** be consistent with the models enabled in Script 3.
    * `N_SEEDS_FOR_EVALUATION`: The number of repetitions (seeds) for the evaluation, used to test model robustness. A value of 3-5 is recommended.
    * `N_SPLITS_OUTER_CV`: The number of folds (k) for the outer cross-validation.
    * `META_LEARNER_N_ITER_BAYESIAN`: The number of Bayesian Optimization iterations for the meta-learner.
    * `N_FEATURES_TO_PLOT`: The number of top features to display in the final feature importance bar chart.
    * `PLOT_SHAP_SWARM_PLOT`: A boolean to enable/disable the generation of the SHAP swarm plot, which is informative but can be time-consuming.

* **📄 Outputs**:
    * **Console Output**: Prints the evaluation results for each seed and a final average performance report across all seeds.
    * `unified_feature_importances_...png`: **(Core Output)** The bar chart for the Two-Level Weighted SHAP global feature importances.
    * `shap_summary_plot_...png`: **(Core Output)** The SHAP summary (swarm) plot, showing the impact of each feature on the model's output.
    * `cv_predictions_pivoted_...xlsx`: Detailed prediction logs, containing the true value and the predicted value from each model for every sample in each fold and seed.
    * `cv_model_scores_per_fold_...xlsx`: Detailed performance logs, containing the R² and RMSE scores for each model in each fold and seed.
    * `shap_swarm_data_...xlsx`: The raw data used to generate the SHAP plots, allowing for deeper analysis.

* **💡 Highlights**:
    * **Stacking Ensemble**: Combines the strengths of multiple powerful learners to typically achieve higher predictive accuracy than any single model.
    * **Two-Level Weighted SHAP Analysis**: A novel weighting scheme that considers the performance of both the base models and the ensemble in each cross-validation fold, yielding a more stable and trustworthy feature importance ranking.

---

### 📜 Script 5: Prediction

* **🎯 Function**:
    * Loads and applies the final trained Stacking Ensemble model.
    * Makes predictions on new, unseen external datasets.
    * Supports two modes: quickly reusing the model already trained by the evaluation script, or retraining a final model on the entire training set before prediction.

* **▶️ Usage**:
    1.  This script should be run after Script 4 to inherit the trained `global_best_meta_learner`.
    2.  Configure the path to the prediction file in the `User Configuration` section.
    3.  Run the script to generate an Excel file with the detailed prediction results.

* **⚙️ Tunable Parameters**:
    * `UNKNOWN_DATA_FILE`: **(Required)** The filename of the Excel file containing the data to be predicted.
    * `UNKNOWN_DATA_FILE_COLUMN_RANGE`: **(Key)** Defines how to extract feature columns from the prediction file. **Must ensure the number and order of extracted features are identical to the training set.**
    * `REUSE_PRETRAINED_STACKING_MODEL`: **(Key)** Set to `True` (recommended) to directly use the model trained in Script 4 for fast predictions. Set to `False` to retrain a new final model.
    * `ENABLED_BASE_LEARNERS`: **Must** be consistent with Scripts 3 and 4.
    * `PREDICTION_OUTPUT_FILENAME_PREFIX`: The prefix for the output prediction file.

* **📄 Outputs**:
    * **Console Output**: Prints status messages for model loading, data processing, and the prediction process.
    * `unknown_predictions_...xlsx` (or `.csv`): **(Core Output)** Contains the predictions for each sample in the unknown dataset. The file includes not only the final Stacking prediction but also the individual predictions from each base model for analysis.

* **💡 Highlights**:
    * **One-Click Prediction**: A highly automated process; the user only needs to provide the new data file to get predictions.
    * **Column Alignment Mechanism**: Robustly handles the new data by automatically aligning its columns with the training data, preventing common errors caused by column name mismatches.

---

## 🗺️ The Workflow

This workflow seamlessly connects five core scripts to guide you from data generation to final prediction, following a clear, sequential path.

 **`Script 1: Molecule Generation & Sampling`**

 *Defines the problem, generates computational tasks*

 `⬇️`

 **`Script 2: Feature Engineering`**

 *Builds the initial database from DFT results, selects optimal features*

 `⬇️`

 **`Script 3: Hyperparameter Optimization`**

 *Finds the optimal "operating state" for each base model*

 `⬇️`

 **`Script 4: Stacking Ensemble & Evaluation`**

 *Assembles the "expert team" and evaluates the final model*

 `⬇️`

 **`Script 5: Prediction`**

 *Applies the trained model to solve real-world problems*

---

## 💻 How to Use

1.  **Environment Setup**:
    Download the scripts and `requirements.txt` file from the desired project folder (e.g., `CPyr/`). Install all dependencies.
    ```bash
    pip install -r requirements.txt
    ```

2.  **Workflow Execution**:
    * **Step 1: Generate Molecular Library**
        * Run `Molecule Generation & Sampling.ipynb` to produce a list of representative molecules (`representative_sample.xlsx`).
        * Perform your own quantum chemistry calculations on this library and compile the results with feature descriptors into a new master Excel file.

    * **Step 2: Engineer Features**
        * Run `Feature Engineering.ipynb` on your master Excel file to generate the optimal feature set (`final_selected_dataset_...xlsx`).

    * **Step 3: Train and Predict**
        * Open and run the cells in `Tree_stacking.ipynb` sequentially. This notebook performs:
            * **Hyperparameter Optimization** (Script 3) on the feature-engineered data.
            * **Stacking Ensemble Evaluation** (Script 4) to build and validate the final model.
            * **Prediction** (Script 5) on new, unseen data.

    ⚠️ **Important Note**: Scripts 3, 4, and 5 are located within `Tree_stacking.ipynb` and must be run in the same session to pass variables (like optimized models) in memory.

---    

## 📦 Environment Setup & Reproducibility

This section outlines the necessary dependencies and provides three distinct methods for environment setup. Please choose the one that best suits your needs.

### 🐍 Python Version

This project was developed and tested using **Python 3.10.18**. While it may work with other Python 3.10+ versions, using this specific version is recommended to maximize reproducibility. The configuration files provided in the options below will automatically install the correct Python version when using Conda.

---

### 📋 Core Dependencies

While the full environment contains numerous packages, the core scientific computing and machine learning libraries used in this project are listed below. The exact versions are managed by the configuration files in the following sections.

```
# Core scientific computing and machine learning libraries
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

# Libraries for Jupyter Notebook integration
jupyterlab>=4.0.0
notebook==7.3.2
ipykernel==6.29.5
ipywidgets==8.1.7
```
---

### Environment Configuration

**⚠️ IMPORTANT NOTE:** For academic review or users who need to reproduce the results from our paper as closely as possible, **it is highly recommended to use Option 1**. Using Option 2 or 3 is more likely to produce numerical differences in the output, even with identical code and random seeds. This is primarily due to the following reasons:

1.  **Dependency Solver Ambiguity** 🧩: `environment.yml` and `requirements.txt` files allow the dependency solver (Conda or Pip) to select different versions of underlying sub-dependencies over time.
2.  **Platform-Specific Builds** 💻: Even with identical package versions, the underlying compiled libraries (e.g., Intel MKL vs. OpenBLAS) can differ across operating systems, causing minute variations in floating-point calculations.
3.  **Sensitivity of Stochastic Optimization** 🎲: This project utilizes Bayesian Optimization. The minor computational variations from the points above can influence the optimization trajectory, leading to different hyperparameter choices and, consequently, different final results.
4.  **Stochasticity from Parallel Computing** ⚡: To accelerate computations, the script may utilize parallel processing (e.g., via `n_jobs=-1`). However, when computations are executed across multiple CPU cores, the precise order of floating-point operations can vary slightly, which can be amplified by iterative algorithms.

---

### 🥇 Option 1: Highest-Fidelity Reproducibility (via `spec-file.txt`)

This method is the most robust approach to maximize the likelihood of reproducing the numerical results. It uses an explicit specification file that locks the exact version, build, and source URL of every software package.

**Platform:** This method is strictly platform-dependent. The provided `spec-file.txt` is for **Windows (x64)** only.

**Instructions:**
1. Ensure you have [Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html) installed.
2. Download the `spec-file.txt` file from this repository.
3. Open your terminal (or Anaconda Prompt) and navigate to the directory where you saved the file.
4. Execute the following command to create the environment:
    ```bash
    conda create --name my-project-env --file spec-file.txt
    ```
5. Activate the newly created environment:
    ```bash
    conda activate my-project-env
    ```

---

### 🥈 Option 2: Cross-Platform Setup (via `environment.yml`)

This method is recommended for general users who wish to run the code on different operating systems (e.g., Linux, macOS). It uses the `environment.yml` file to create a functionally equivalent environment but may produce minor numerical differences from the original results.

**Instructions:**
1. Ensure you have [Conda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html) installed.
2. Download the `environment.yml` file from this repository.
3. Open your terminal (or Anaconda Prompt) and navigate to the directory where you saved the file.
4. Execute the following command to create the environment:
    ```bash
    conda env create -f environment.yml -n my-project-env
    ```
5. Activate the environment:
    ```bash
    conda activate my-project-env
    ```

---

### 🥉 Option 3: Basic Setup (via `requirements.txt`)

This option is provided for users who prefer to work with standard Python virtual environments (`venv`) and `pip`. This method also does not ensure exact numerical reproducibility.

**Instructions:**
1. Download the `requirements.txt` file from this repository.
2. Open your terminal and navigate to the directory where you saved the file.
3. Create and activate a Python virtual environment:
    ```bash
    python -m venv venv
    # On Windows, activate with:
    # venv\Scripts\activate
    # On Linux/macOS, activate with:
    # source venv/bin/activate
    ```
4. Run the following command to install all dependencies (including Jupyter for running notebooks):
    ```bash
    pip install -r requirements.txt jupyterlab
    ```

---

## 📜 License and Correspondence

The data and code in this repository are available under the [MIT License](https://github.com/Gremelody/intelligent-molecular-skeleton-design/blob/main/LICENSE).

For any inquiries or if you use this workflow in your research, please correspond with Prof. [Guangmin Zhou](mailto:guangminzhou@sz.tsinghua.edu.cn) 📧.

---

## 🙏 Acknowledgements

[Yifei Zhu](zhuyifeiedu@126.com) at Tsinghua University conceived and formulated the algorithms, built the quantum chemistry database, developed and deposited the code, and authored this comprehensive guideline document.
