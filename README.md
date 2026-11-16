# Machine Learning Prediction of Xenon Adsorption in Nanoporous Materials

### A Comparative Study of Random Forest, Gradient Boosting, and Gaussian Process Regression

### **EEL 6843 — Final Project**  
### **Yuxuan Wu**  
Department of Materials Science and Engineering, University of Florida

> **Project status:** In progress. This course project uses public data only. 

## 1. Overview and Research Questions

### 1.1 Background and motivation

Xenon (Xe) and krypton (Kr) are noble gases with similar physical properties, making their separation challenging. Selective adsorption in nanoporous materials is a promising approach for gas capture and separation. The adsorption behavior depends on both accessible pore geometry and host–guest interactions. Studies of zeolites have demonstrated that framework composition, local polarity, and extra-framework cations can affect Xe/Kr separation [2, 3].

Computational adsorption screening can evaluate many candidate materials, but repeated molecular simulations are resource-intensive. Publicly available adsorption datasets make it possible to train supervised machine-learning models that relate material descriptors and operating conditions to Xe adsorption performance [1]. Recent work has also explored coupling machine learning with molecular simulations for Xe/Kr adsorbent screening [4].

### 1.2 Research questions

1. Can supervised regression models predict Xe adsorption uptake from publicly available material descriptors and adsorption conditions?
2. How do Random Forest, Gradient Boosting, and Gaussian Process Regression compare in predictive accuracy and generalization to previously unseen materials?
3. Which available material descriptors and operating conditions are most strongly associated with predicted Xe uptake?

## 2. Dataset

### 2.1 Public data source

**Primary candidate:** [MOFX-DB — Download Databases](https://mof.tech.northwestern.edu/databases), specifically the **hMOF — Krypton/Xenon** download. MOFX-DB aggregates simulated adsorption results, structural files, and textural properties for nanoporous materials [1]. 

**Important distinction:** The MOFX-DB download page lists Xe/Kr downloads for hMOFs and the Tobacco collection, but its IZA section currently lists a hydrogen download. This project therefore does not assume that a downloadable Xe-in-zeolites subset is included in IZA.

### 2.2 Input features and target

The final feature set will depend on the fields present and joinable in the public data. Candidate predictors include:

- Pore-size and textural descriptors, **if provided and matched to material IDs**
- Surface area and accessible pore volume, **if available**
- Adsorption pressure and temperature, **if varied and reported**
- Other documented numerical material descriptors

**Prediction target (y):** Xe adsorption uptake under the recorded conditions.  
**Input matrix (X):** Available features after validation and preprocessing.

Column meanings and units will be checked against the source documentation before modeling.

## 3. Exploratory Data Analysis (EDA)

### 3.1 Dataset overview

Report the shape of the dataset, data types, number of unique materials, missing-value counts, duplicate records, and summary statistics (`describe()`). Distinguish duplicate rows from legitimate measurements or simulations of the same material at different conditions.

### 3.2 Distributions

Inspect the Xe uptake distribution and key numerical features with histograms and boxplots. Describe whether variables are symmetric, right-skewed, clustered, or dominated by extreme values. Use appropriate units and note any transformations proposed for modeling.

### 3.3 Correlations and feature–target relationships

Use a correlation heatmap for numerical features and scatterplots for selected feature–target pairs. Correlation is exploratory and does not establish causation; also check for nonlinear trends and correlated descriptors.

### 3.4 Outliers and data quality

Flag unusual uptake values and descriptors using distribution plots and domain-specific checks. Do not automatically discard high-uptake candidates; verify units, physical plausibility, identifiers, and simulation conditions first.

## 4. Data Preprocessing

### 4.1 Cleaning and feature selection

- Parse the public isotherm/adsorption files and join them to material descriptors using validated IDs.
- Resolve units and document any conversion.
- Inspect missing values and duplicated identifiers or records.
- Exclude metadata or target-derived columns that would cause label leakage.
- Record the final feature list and sample counts.

### 4.2 Train/test separation

Use a held-out test set (provisionally 20%) with a **grouped split by material ID** so that measurements at different pressures for the same material do not appear in both training and test sets. Fit imputation and scaling **only on training data** within scikit-learn pipelines. Use grouped cross-validation when records from the same material repeat.

### 4.3 Scaling and transformations

Evaluate whether standardization is needed for a particular estimator (especially Gaussian Process Regression). Tree-based models typically do not require standardization. If a logarithmic transform of Xe uptake is used, document how predictions are transformed back to original units before evaluating errors.

## 5. Machine Learning Models

Three supervised regression methods will be compared using consistent features and data splits.

### 5.1 Random Forest Regression (RF)

An ensemble of decision trees that can capture nonlinear interactions among material features. It provides a useful baseline and model-level feature importance estimates.

### 5.2 Gradient Boosting Regression (GB)

A sequential ensemble of decision trees trained to reduce prediction errors, with tunable model complexity and learning rate.

### 5.3 Gaussian Process Regression (GPR)

A probabilistic regression method that can provide both a predicted mean and an uncertainty estimate. Because its training cost scales poorly with dataset size, GPR may require a documented smaller training subset or an alternative approximation if the downloaded data are too large.

## 6. Hyperparameter Tuning and Validation

Hyperparameters will be tuned with `GridSearchCV` or `RandomizedSearchCV` using **group-aware cross-validation** where multiple observations share a material ID. A possible search space includes:

| Model | Candidate hyperparameters |
|---|---|
| Random Forest | `n_estimators`, `max_depth`, `min_samples_leaf`, `max_features` |
| Gradient Boosting | `n_estimators`, `learning_rate`, `max_depth`, `subsample` |
| GPR | kernel choice, kernel length scale, `alpha` / noise level |

The final tuning grids, cross-validation folds, scoring rule, and random seed will be reported after implementation. The test set will remain untouched until final evaluation.

## 7. Results and Model Evaluation

Performance will be reported on the held-out test set with metrics in the units of Xe uptake where applicable:

- **Mean Absolute Error (MAE)**
- **Root Mean Squared Error (RMSE)**
- **Coefficient of Determination (R²)**

Results will be summarized in a model-comparison table and predicted-versus-observed and residual plots once the notebook analyses are complete. Feature importance will be evaluated where appropriate, using methods such as permutation importance on held-out data.

The discussion will distinguish validation performance from the final held-out test results, report outliers, and indicate whether any models systematically underpredict high uptake values.

## 8. Discussion


## 9. Expected Contributions and Future Work


## 10. Reproducibility and Data Access

The analysis is designed to use publicly accessible adsorption data from [MOFX-DB](https://mof.tech.northwestern.edu/databases). 

## 11. References and Literature Context

1. Bobbitt, N. S., et al. (2023). **MOFX-DB: An Online Database of Computational Adsorption Data for Nanoporous Materials.** *Journal of Chemical & Engineering Data*, 68(2), 483–498. https://doi.org/10.1021/acs.jced.2c00583  
   *Use in this project:* Source and description of public molecular-simulation adsorption data.

2. **Control of Zeolite Local Polarity toward Efficient Xenon/Krypton Separation.** (2024). *Journal of the American Chemical Society*, 146(12), 8335–8342. https://doi.org/10.1021/jacs.3c13994  
   *Use in this project:* Scientific motivation for how local pore chemistry and charge environment affect Xe/Kr separation.

3. Chen, D., et al. (2025). **Size-Inverse Molecular Sieving Xenon/Krypton Separation through Cation-Tuned Gating Effect within Linde Type A Zeolites.** *Nature Communications*, 16, 9849. https://doi.org/10.1038/s41467-025-64823-y  
   *Use in this project:* Physical insight into pore gating, adsorption selectivity, and the limits of geometry-only descriptors.

4. **Synergistic Machine Learning and Molecular Simulation for Xenon Adsorption and Separation: Mechanisms, Screening, and Performance Validation.** (2026). *Chemical Engineering Science*, 335, 124481. https://doi.org/10.1016/j.ces.2026.124481  
   *Use in this project:* Related ML-based adsorbent screening work and motivation for benchmarking regression models.

## Acknowledgments

This independent course exercise uses publicly accessible literature and data only. It does not reproduce or disclose unpublished research-group datasets, slide figures, or internal results.
