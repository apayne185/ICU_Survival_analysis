# Breast Cancer Survival Analysis

This project progresses from cohort curation and Kaplan-Meier exploration to multivariable Cox proportional hazards modeling and ML models evaluated at clinically actionable horizons in order to analyze the survival of Breast Cancer Patients.



## Repository structure

This repository contains the core analysis notebook and utility scripts:

- `BC_survival.ipynb`: The main Jupyter Notebook containing the full analysis, from data loading to model evaluation.
- `utils.py`: A collection of helper functions for plotting, preprocessing, modeling, and evaluation.
- `environment.yml`: A Conda environment file to ensure reproducibility of the analysis.




## Dataset

The analysis is performed on a breast cancer cohort (ASK ANNA WHERE SHE GOT THE DATASET).

- **Features**: Key clinical and pathological variables such as patient age, tumor size, grade, lymph node status, and hormone receptor status (ER, PR, HER2).
- **Outcomes**: The primary outcomes are survival time (`duration`) and an event indicator (`event`) for death. The analysis also supports competing risks, such as death from other causes versus death from breast cancer.
- **Censoring**: Patients lost to follow-up or alive at the end of the study period are considered censored.



## Environment and reproducibility

- Python 3.11 recommended
- Key libraries include`matplotlib`, `scikit-learn`, `lifelines`, `scikit-survival`, ...
- To ensure reproducible environment, use the provided `environment.yml` file with Conda (located on root directory), which contains all necessary libraries:
  ```bash
  conda env create -f environment.yml
  conda activate breast-cancer-survival
  jupyter lab
  ```



## Workflow at a glance

The analysis is structured to provide a comprehensive view of patient survival through combining traditional biostatistics with ML.

| Section | Primary Focus | Representative Outputs |
|---|---|---|
| **1. Exploratory Data Analysis** | Data validation, censoring checks, Kaplan-Meier curves, cumulative incidence functions (CIF), log-rank tests for subgroups. | Survival probabilities at 1, 5, 10 years; median survival time; comparisons by tumor grade or hormone receptor status. |
| **2. Cox Proportional Hazards** | Univariable and multivariable Cox modeling, checking proportional hazards assumption, interpreting hazard ratios. | Hazard Ratios (HR) for key predictors (age, tumor size), concordance index, time-dependent AUROC, calibration analysis. |
| **3. Fixed-Horizon Prediction** | Decision Tree and Random Forest models for predicting survival at specific time points (5-year survival). | AUROC, AUPRC, and Brier scores at 1, 5, and 10-year horizons; decision curve analysis for clinical utility. |



## End to end process

### 1. Cohort QC and Kaplan-Meier Exploration

This initial phase establishes a baseline understanding of the cohort's survival characteristics.

**Practical Use**:
- Use Kaplan-Meier curves as a non-parametric baseline for risk communication and for sanity-checking later model outputs.
- Identify significant prognostic subgroups (based on tumor grade) that may require stratified analysis or tailored treatment strategies.



### 2. Cox Proportional Hazards Modeling

This section builds interpretable models to understand how different clinical factors contribute to patient risk over time.

**Practical Use**:
- Use the Hazard Ratios from the Cox model to explain the magnitude and direction of risk associated with factors like tumor size or lymph node involvement.
- The model can inform clinical pathway design and feature prioritization for future research.



### 3. Fixed-Horizon Prediction Models

This final phase focuses on building high-performance machine learning models to predict individual patient outcomes at fixed, clinically relevant time points (for instance, 5-year survival).

**Practical Use**:
- Use Random Forest models for maximal predictive accuracy when creating risk stratification tools.
- Use Decision Trees when simple, transparent if-then rules are preferred for clinical decision support.
- Decision curve analysis helps select risk thresholds that align with clinical priorities, balancing the trade-off between true positives and false positives.



## Model comparison and selection

- The Cox model provides the best interpretability for understanding prognostic factors.
- Random Forest models typically offer the highest discrimination (AUROC) for fixed-horizon prediction.
- All models are evaluated for discrimination and calibration to ensure that predictions are accurate in ranking patients and also reliable in their absolute risk estimates.



## How to reproduce

1.  Set up the Conda environment using `environment.yml`.
2.  Launch JupyterLab and open `BC_survival.ipynb`.
3.  Update the dataset path at the top of the notebook.
4.  Run the cells in order to reproduce the analysis and review the outputs and added explanations.




## Outputs to expect

- Kaplan-Meier plots with survival estimates and subgroup comparisons.
- Cox model summary table with hazard ratios, p-values, and concordance index.
- Calibration plots and Brier scores for all models.
- Performance tables for machine learning models, including AUROC and AUPRC by horizon.
- Decision curve analysis plots to assess clinical utility.   




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
