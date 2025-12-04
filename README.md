# Breast Cancer Survival Analysis with METABRIC Dataset

## Introduction and Purpose

This repository provides a comprehensive pipeline for survival analysis using the METABRIC (Molecular Taxonomy of Breast Cancer International Consortium) dataset. Breast cancer is the most prevalent cancer in women globally, and survival analysis is crucial for understanding patient prognosis, evaluating treatment efficacy, and identifying key risk factors.

### Clinical Objectives
The primary goals of this analysis are:
1.  **Prognostic Modeling**: Develop robust models to predict overall survival (OS) and relapse-free survival (RFS) in breast cancer patients.
2.  **Risk Stratification**: Identify distinct patient subgroups based on their survival patterns to enable personalized risk assessment.
3.  **Feature Importance**: Determine which clinical and molecular features most significantly influence survival outcomes.
4.  **Treatment Insights**: Explore associations between different treatment modalities (chemotherapy, hormone therapy, radiotherapy) and patient survival.

The insights from this project can help clinicians make more informed treatment decisions, support patient counseling, and provide a basis for future clinical trial design.

## Project Structure

-   `BC_SURVIVAL.ipynb`: The main Jupyter notebook containing the complete end to end survival analysis pipeline, from data preprocessing to model evaluation.
-   `data/`: Directory containing the dataset.
    -   `Breast Cancer METABRIC.csv`: The raw dataset used for the analysis.
-   `environment.yml`: Conda environment file listing all necessary dependencies to ensure reproducibility.
-   `README.md`: This file, providing an overview of the project.

## Installation and Environment Setup

To ensure a reproducible environment, all dependencies are specified in the `environment.yml` file.

1.  **Create the Conda Environment**:
    Open the terminal and run the following command to create the `bc-survival` conda environment:
    ```bash
    conda env create --name bc-survival --file environment.yml
    ```

2.  **Activate the Environment**:
    Once the environment is created, activate it using:
    ```bash
    conda activate bc-survival
    ```

3.  **Launch Jupyter**:
    With the environment activated, launch Jupyter Notebook or JupyterLab to run the analysis:
    ```bash
    jupyter notebook
    ```

## Quickstart Workflow

1.  Ensure installation is complete, environment setup steps above.
2.  Open and run the `BC_SURVIVAL.ipynb` notebook from top to bottom. The notebook is structured to execute the entire pipeline.

## Data Description
The METABRIC dataset contains clinical and molecular data for 2,509 breast cancer patients. After cleaning and removing subjects with missing survival outcomes, the final cohort for analysis consists of 1,980 patients.

-   **Target Variables**:
    -   **Overall Survival (OS)**: The primary outcome, defined by `duration_os` (time in months from diagnosis to death) and `event_os` (1 if deceased, 0 if censored/living).
    -   **Relapse-Free Survival (RFS)**: A secondary outcome, defined by `duration_rfs` (time in months to recurrence) and `event_rfs` (1 if recurred, 0 if not).

-   **Features**: The dataset includes a set of clinical and molecular features, such as:
    -   **Patient Demographics**: Age at diagnosis, menopausal state.
    -   **Tumor Characteristics**: Tumor size, stage, histologic grade, cellularity, and molecular subtypes (PAM50, 3-Gene classifier).
    -   **Biomarkers**: Estrogen Receptor (ER), Progesterone Receptor (PR), and HER2 status.
    -   **Treatment History**: Information on if the patient received chemotherapy, hormone therapy, or radiotherapy.


## Model Evaluation and Decision Policies
Model performance is rigorously assessed using metrics that capture discrimination, calibration, and clinical utility.

* Survival (Concordance Index (C-index)): Measures the rank correlation between predicted risk and actual survival time (used for Cox Model).
* Fixed-Horizon (AUROC (Area Under the ROC Curve)): "Measures model discrimination at a fixed time horizon (60 month)."
* Fixed-Horizon (AUPRC (Area Under the Precision-Recall Curve)): Useful for imbalanced datasets; superior to AUROC when the event rate (prevalence) is low.
* Calibration (Brier Score Loss): Measures the average squared difference between predicted risk and actual outcome.

### Calibration
Models (CPH, DT, RF) are trained to predict risk, and subsequently, their raw risk scores are passed through Isotonic Regression for calibration. The resulting calibrated risks ( artifacts) are used for final metric calculations and visualized using a Calibration Curve (60 month horizon) to ensure the predicted probabilities match the observed event rates_cal. 



## Survival Analysis Workflow

The notebook implements a multi-stage survival analysis workflow.

### 1. Data Preprocessing and Leakage Control
-   **Data Cleaning**: Redundant columns are removed, and patients with missing survival outcomes are excluded.
-   **Leakage Prevention**: A strict separation is maintained between features (X) and outcomes (y). Outcome-related columns are removed from the feature set before any modeling or preprocessing to prevent data leakage.
-   **Data Splitting**: The data is split into stratified training (60%), validation (20%), and test (20%) sets. Stratification is performed on the event status to ensure a balanced distribution of outcomes across all sets.
-   **Preprocessing Pipeline**: `scikit-learn` pipeline is constructed to handle missing values (imputation), encode categorical features (one hot encoding), and scale numeric features (standardization). This pipeline is fitted only on the training data and then applied to the validation and test sets to prevent leakage.

### 2. Exploratory Data Analysis (EDA)
-   **Feature Distributions**: Univariate analysis of numeric (histograms, box plots) and categorical (bar charts) features to understand their distributions and identify skewness or imbalances.
-   **Bivariate Analysis**: The relationship between each feature and the survival outcome is explored. Statistical tests (Mann-Whitney U for numeric, Chi-square for categorical) are used to identify features that differ significantly between patients who experienced an event versus those who were censored.

### 3. Kaplan-Meier (KM) Survival Analysis
-   **Overall Survival (OS) Curve**: A KM curve is generated for the entire cohort to estimate the overall survival probability over time.
-   **Relapse-Free Survival (RFS) Curve**: A similar analysis is performed for RFS to estimate the probability of remaining recurrence-free.
-   **Median Survival & RMST**: For both OS and RFS, the **median survival time** and **Restricted Mean Survival Time (RMST)** are calculated. RMST provides a robust measure of average survival time up to a specific time point.

### 4. Cox Proportional Hazards Modeling

This section builds interpretable models to understand how different clinical factors contribute to patient risk over time.

**Practical Use**:
- Use the Hazard Ratios from the Cox model to explain the magnitude and direction of risk associated with factors like tumor size or lymph node involvement.
- The model can inform clinical pathway design and feature prioritization for future research.



### 5. Fixed-Horizon Prediction Models

This phase focuses on building high-performance machine learning models to predict individual patient outcomes at fixed, clinically relevant time points (for instance, 5-year survival).

**Practical Use**:
- Use Random Forest models for maximal predictive accuracy when creating risk stratification tools.
- Use Decision Trees when simple, transparent if-then rules are preferred for clinical decision support.
- Decision curve analysis helps select risk thresholds that align with clinical priorities, balancing the trade-off between true positives and false positives.


### 6. Calibration, Brier at Fixed Horizons, Threshold Selection

This phase consists of post-processing model outputs to ensure reliability and translating statistical risks into actionable clinical decisions.

**Practical Use:**
* The calibrated models and their Brier scores provide the most trustworthy risk estimates for patient communication.
* DCA allows clinicians to select an optimal risk threshold that balances the harm of unnecessary treatment (FPs) against the benefit of early intervention (TPs). This ensures the model's output directly supports a utility driven clinical policy.




## Outputs to expect

- KM plots with survival estimates and subgroup comparisons.
- Cox model summary table with hazard ratios, p-values, and concordance index.
- Calibration plots and Brier scores for all models.
- Performance tables for all models (Cox, Decision Tree, Random Forest), summarizing calibrated AUROC, AUPRC, and Brier scores across time horizons (60, 120, 180).-
- Decision curve analysis plots to assess clinical utility.  
- Grouped Bar Charts comparing model performance (AUROC and AUPRC) across all horizons. 



## Extending the analysis

- **External Validation**: Swap in a different breast cancer cohort to validate the models' generalizability.
- **Feature Engineering**: Incorporate genomic data (gene expression, etc.) or treatment information to help improve predictive performance.
- **Advanced Models**: Experiment with other survival models like Gradient Boosted Trees (XGBoost) or deep learning approaches.




## Troubleshooting

- Ensure `scikit-survival` is installed from `conda-forge` to avoid compilation issues.
- If running into memory issues with large datasets, use `pandas.read_csv` with the `usecols` parameter to load only necessary columns.

## Ethical use and limitations

- These models are trained on historical data and are intended for research and decision support, not as a substitute for clinical judgment.
- Model performance may vary on different populations. Local validation and calibration are essential before any clinical application.
- It is crucial to review model performance across relevant demographic and clinical subgroups to ensure fairness and equity.

## License and citation

- Code under the MIT License
- The code in this repository is available under the MIT License.
- Please cite the original source of the dataset used in analysis.
