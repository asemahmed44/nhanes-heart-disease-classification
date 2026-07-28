# Model Card: NHANES Heart Disease Research Classifier

## Model Details

- **Model type:** Logistic Regression with preprocessing pipeline
- **Task:** Binary classification of self-reported heart disease status
- **Training data:** Preprocessed dataset derived from NHANES 2017–2018
- **Training records:** 4,454
- **Final test records:** 1,114
- **Positive training cases:** 422
- **Positive test cases:** 105
- **Primary selection metric:** Cross-validated PR-AUC
- **Operating threshold:** Approximately 0.102, selected from training out-of-fold predictions

## Intended Use

This model is intended only for:

- Educational machine-learning demonstrations
- Reproducible data science portfolio work
- Studying imbalanced classification, threshold selection, and model evaluation

## Out-of-Scope Uses

Do not use this model for:

- Medical diagnosis
- Clinical screening or triage
- Treatment or referral decisions
- Insurance, employment, or eligibility decisions
- Individual risk communication
- Population prevalence estimation
- Public-health policy decisions

## Inputs

The model expects 18 variables covering:

- Age and poverty-income ratio
- Sex, race/ethnicity, and education codes
- Medication-use indicators
- Diabetes and prediabetes history
- Stroke and family-history indicators
- Smoking and physical activity
- Hypertension and high cholesterol

## Final Test Performance

Probability metrics:

| Metric | Value |
|---|---:|
| ROC-AUC | 0.8575 |
| PR-AUC | 0.3895 |
| Brier Score | 0.0709 |

At the selected research threshold:

| Metric | Value |
|---|---:|
| Sensitivity | 0.7619 |
| Specificity | 0.7909 |
| Precision | 0.2749 |
| Negative Predictive Value | 0.9696 |
| Balanced Accuracy | 0.7764 |
| F2 | 0.5626 |

These estimates come from one stratified holdout split and are subject to sampling uncertainty.

## Threshold Trade-Off

The selected threshold was chosen to target approximately 80% sensitivity on out-of-fold training predictions. On the unseen test set, sensitivity was 76.2%.

The resulting test confusion matrix was:

- True negatives: 798
- False positives: 211
- False negatives: 25
- True positives: 80

This operating point generates many false positives and has not been clinically validated.

## Data and Evaluation Limitations

- The target is self-reported and its derivation is not documented in the supplied file.
- The dataset lacks NHANES survey weights, strata, PSU variables, and respondent identifiers.
- Results are not nationally representative.
- Identical rows cannot be verified as duplicates without respondent IDs.
- The model predicts prevalent self-reported status, not future disease.
- Diagnosis and medication-history features may reflect prior healthcare access.
- No external or prospective validation was performed.
- Subgroup sample sizes are too small for definitive fairness claims.
- Performance may not generalize to different populations, time periods, or care settings.

## Ethical Considerations

False negatives could create false reassurance. False positives could cause anxiety, unnecessary follow-up, or inappropriate resource use. Use of race/ethnicity and socioeconomic variables requires explicit justification, governance, and fairness review. Model outputs must never be presented as medical advice.

## Monitoring Requirements for Any Future Research Extension

- Reassess ROC-AUC, PR-AUC, calibration, sensitivity, specificity, and PPV.
- Monitor data drift and missingness.
- Evaluate performance across clinically relevant subgroups with adequate sample sizes.
- Revalidate the threshold for the intended setting.
- Document human oversight and an escalation pathway.
- Stop use if calibration or subgroup performance deteriorates.

