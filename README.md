# NHANES Heart Disease Classification

An end-to-end data science project that classifies **self-reported heart disease status** using demographic, socioeconomic, behavioral, medication, and medical-history variables from a preprocessed dataset derived from the 2017–2018 National Health and Nutrition Examination Survey (NHANES).

> **Medical disclaimer:** This repository is for education and research only. It is not a medical device, clinical risk calculator, or diagnostic system and must not be used to make healthcare decisions.

## Project Summary

- **Problem type:** Imbalanced binary classification
- **Records:** 5,568
- **Input features:** 18
- **Positive cases:** 527
- **Positive rate:** 9.46%
- **Primary selection metric:** PR-AUC
- **Selected model:** Logistic Regression
- **Final test ROC-AUC:** 0.8575
- **Final test PR-AUC:** 0.3895
- **Selected research threshold:** approximately 0.102
- **Sensitivity at selected threshold:** 76.2%
- **Specificity at selected threshold:** 79.1%
- **Precision at selected threshold:** 27.5%
- **Negative predictive value:** 97.0%

The threshold was selected from out-of-fold training probabilities to target approximately 80% sensitivity. Test sensitivity is lower because the test set is unseen and contains sampling variation.

## Repository Structure

```text
heart-disease-ds-project/
├── data/
│   └── nhanes_2017_2018_heart_disease_prediction.csv
├── notebooks/
│   └── NHANES_Heart_Disease_Data_Science_Project.ipynb
├── MODEL_CARD.md
├── README.md
├── requirements.txt
└── .gitignore
```

## Dataset

The supplied file contains:

- Demographic variables: age, sex, and race/ethnicity
- Socioeconomic variables: education and poverty-income ratio
- Medication indicators
- Diabetes and prediabetes indicators
- Stroke and family-history indicators
- Smoking and physical-activity indicators
- Hypertension and high-cholesterol indicators
- A binary `heart_disease` target

The file is already heavily preprocessed. It does not contain the original respondent identifier, survey weights, strata, or primary sampling unit variables.

### Important NHANES Limitation

NHANES uses a complex, multistage probability sample rather than a simple random sample. CDC recommends using sampling weights and design variables for NHANES analyses because ignoring them can bias population estimates and overstate statistical significance. Those fields are absent from this supplied file, so:

- The observed target rate is not a nationally representative prevalence estimate.
- The model is evaluated as an educational predictive exercise only.
- Subgroup comparisons are descriptive and uncertain.
- Survey-correct inference cannot be performed.

Official references:

- [CDC NHANES Sample Design Tutorial](https://wwwn.cdc.gov/nchs/nhanes/tutorials/SampleDesign.aspx)
- [NHANES 2017–2018 Questionnaire Instruments](https://wwwn.cdc.gov/nchs/nhanes/continuousnhanes/questionnaires.aspx?Cycle=2017-2018)
- [NHANES 2017–2018 Demographics Documentation](https://wwwn.cdc.gov/Nchs/Data/Nhanes/Public/2017/DataFiles/DEMO_J.htm)

## Data Quality Decisions

- No missing values are present in the supplied file.
- Fifty-eight rows are fully identical.
- Identical rows are retained because the dataset has no respondent ID; they may represent different respondents with identical answers.
- `sex`, `race_ethnicity`, and `education` are treated as categorical codes rather than continuous numbers.
- Age and poverty-income ratio are treated as numeric features.
- Binary indicators are validated to contain only zero and one.

## Leakage-Safe Workflow

1. Validate schema, allowed values, and numeric ranges.
2. Create an untouched stratified 20% final test set.
3. Perform target-aware EDA using training data only.
4. Place imputation, scaling, and one-hot encoding inside a scikit-learn pipeline.
5. Compare models using stratified five-fold cross-validation.
6. Select the model using mean PR-AUC.
7. Generate out-of-fold training probabilities.
8. Select a recall-oriented threshold from training data only.
9. Fit the final pipeline on the complete training set.
10. Evaluate the final test set once.
11. Report bootstrap uncertainty intervals and subgroup diagnostics.
12. Save the complete preprocessing and model artifact.

## Models Compared

- Dummy prior baseline
- Logistic Regression
- Random Forest
- Extra Trees
- Histogram Gradient Boosting

### Cross-Validated Results

| Model | ROC-AUC | PR-AUC |
|---|---:|---:|
| Logistic Regression | 0.8726 | 0.4238 |
| Random Forest | 0.8680 | 0.3792 |
| Histogram Gradient Boosting | 0.8513 | 0.3576 |
| Extra Trees | 0.8514 | 0.3485 |
| Dummy Prior | 0.5000 | approximately 0.095 |

Logistic Regression was selected because it achieved the highest mean cross-validated PR-AUC while remaining interpretable.

## Final Test Results

### Probability Metrics

| Metric | Score |
|---|---:|
| ROC-AUC | 0.8575 |
| PR-AUC | 0.3895 |
| Brier Score | 0.0709 |

### Selected Threshold Results

| Metric | Score |
|---|---:|
| Sensitivity / Recall | 0.7619 |
| Specificity | 0.7909 |
| Precision / PPV | 0.2749 |
| Negative Predictive Value | 0.9696 |
| Balanced Accuracy | 0.7764 |
| F1 | 0.4040 |
| F2 | 0.5626 |
| Matthews Correlation Coefficient | 0.3677 |

Confusion matrix at the selected threshold:

|  | Predicted Negative | Predicted Positive |
|---|---:|---:|
| Actual Negative | 798 | 211 |
| Actual Positive | 25 | 80 |

The lower threshold increases sensitivity compared with the default 0.50 threshold, but it also increases false positives. This trade-off is not clinically validated.

## Evaluation and Explainability

The notebook includes:

- Target and feature-distribution EDA
- ROC and precision–recall curves
- Calibration curve and Brier score
- Confusion matrix
- Default versus recall-oriented threshold comparison
- Bootstrap 95% intervals for ROC-AUC and PR-AUC
- Logistic coefficients and conditional odds ratios
- Sex and race/ethnicity subgroup diagnostics

Model coefficients show conditional associations within the fitted model. They must not be interpreted as causal effects or clinical risk ratios.

## Running in Google Colab

1. Open `notebooks/NHANES_Heart_Disease_Data_Science_Project.ipynb` in Colab.
2. Select **Runtime → Run all**.
3. Upload `nhanes_2017_2018_heart_disease_prediction.csv` if prompted.
4. Run all cells to reproduce analysis, training, evaluation, and the saved artifact.

## Running Locally

```bash
git clone <your-repository-url>
cd heart-disease-ds-project
pip install -r requirements.txt
jupyter notebook
```

Open the notebook from the `notebooks` directory and run all cells.

## Saved Artifact

Running the notebook creates:

```text
nhanes_heart_disease_research_model.joblib
```

It contains:

- The fitted preprocessing pipeline
- The fitted classifier
- The selected research threshold
- Expected feature columns
- Feature-group definitions
- Intended-use metadata

## Responsible Use and Limitations

- The target is self-reported and its exact derivation is not documented in the supplied file.
- The project predicts current self-reported status, not future disease incidence.
- Medication and diagnosis-history features can encode prior healthcare access and contact.
- The dataset does not include clinical measurements such as ECG, laboratory values, imaging, or clinician adjudication.
- The file omits NHANES weights and sample-design variables.
- Subgroup sample sizes and positive counts are small.
- External and prospective validation are required before any real-world use.
- The model must not replace professional medical assessment.

## Potential Improvements

- Rebuild the dataset directly from documented NHANES source files.
- Preserve respondent IDs for traceability and duplicate assessment.
- Include survey weights, strata, and PSU variables for population analysis.
- Verify and document the target definition.
- Add laboratory and examination variables where scientifically justified.
- Use nested cross-validation and external validation.
- Evaluate temporal and geographic transportability.
- Define a clinically justified operating threshold and decision-curve analysis.
- Monitor calibration and subgroup performance on new data.

## Technology

- Python
- pandas
- NumPy
- scikit-learn
- Matplotlib
- Seaborn
- Joblib
- Jupyter Notebook / Google Colab

## Data Attribution and License

Before publishing, verify the upstream source, target-construction process, and redistribution terms for the preprocessed CSV. Add the exact dataset citation and license. The official NHANES documentation links above describe the source survey but do not by themselves document this derived file.

