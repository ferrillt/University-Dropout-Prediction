# University Dropout Prediction

## Project overview

This project uses machine learning to identify students who may be at risk of withdrawing from an online university course. The analysis compares Logistic Regression, Random Forest, and XGBoost using demographic, academic, and course-enrollment information from the Open University Learning Analytics Dataset (OULAD).

The project emphasizes recall because failing to identify an at-risk student may prevent an institution from offering timely support. Logistic Regression provided the best combination of recall, overall discrimination, and interpretability for this use case.

## Research question

Can student demographic, academic, and enrollment characteristics help identify students who are most at risk of withdrawing?

## Data

The completed analysis uses `studentInfo.csv`, which contains 32,593 student-module-presentation records and 12 original fields. The variables describe course information, demographics, prior education, previous attempts, studied credits, and final results.

The target variable, `is_withdrawn`, was created from `final_result`:

- `1`: Withdrawn
- `0`: Pass, Fail, or Distinction

The data contains 10,156 withdrawal records, representing 31.16% of the observations. The `imd_band` field contains 1,111 missing values. No duplicate rows were found.

`studentRegistration.csv` accompanies the project files but is not used by the completed notebook or the reported models. Registration timing is a potential source of additional features for future development.

## Analysis process

The notebook performs the following steps:

1. Loads and audits the student information data.
2. Defines withdrawal as a binary classification target.
3. Examines class balance and withdrawal patterns across student groups.
4. Removes the student identifier from the model features.
5. Applies one-hot encoding to categorical variables.
6. Creates a stratified 70% training and 30% test split.
7. Trains Logistic Regression, Random Forest, and XGBoost classifiers.
8. Compares accuracy, precision, recall, F1 score, and ROC-AUC.
9. Reviews model coefficients and Random Forest feature importance.
10. Evaluates precision, recall, and false-positive rates across demographic subgroups.

Class weighting was used for Logistic Regression and Random Forest. XGBoost used `scale_pos_weight` to place additional emphasis on withdrawal cases.

## Model results

| Model | Accuracy | Precision | Recall | F1 score | ROC-AUC |
| --- | ---: | ---: | ---: | ---: | ---: |
| Logistic Regression | 0.629 | 0.435 | **0.631** | **0.515** | **0.681** |
| XGBoost | 0.633 | **0.437** | 0.616 | 0.511 | 0.678 |
| Random Forest | **0.649** | 0.411 | 0.296 | 0.344 | 0.608 |

Logistic Regression was selected because it identified 63.1% of the students who withdrew and achieved the highest ROC-AUC. Random Forest produced the highest accuracy, but its recall of 29.6% meant it missed most withdrawal cases.

For Logistic Regression, the test-set confusion matrix was:

|  | Predicted non-withdrawal | Predicted withdrawal |
| --- | ---: | ---: |
| Actual non-withdrawal | 4,228 | 2,503 |
| Actual withdrawal | 1,123 | 1,924 |

## Findings

Course module, prior education, disability status, presentation period, and socioeconomic indicators contributed to the Logistic Regression predictions. Several course-module variables had the largest positive coefficients. Students with no formal qualifications or education below A-level also had higher predicted withdrawal likelihood relative to the reference categories.

These coefficients describe associations within this dataset. They do not establish that any characteristic causes withdrawal.

The subgroup review identified meaningful differences in recall and false-positive rates. These differences reinforce the need for fairness review, threshold testing, and continued monitoring before any institutional use.

## Responsible use

The model should support advising, tutoring referrals, and other constructive student services. It should not determine admission, enrollment eligibility, financial aid, or disciplinary action.

Predictions require human review because the data may reflect historical or structural inequalities. Institutions should protect student privacy, explain how predictions are used, monitor performance across groups, and provide students with appropriate safeguards.

## Limitations

- The analysis uses a random record-level split. The same student may appear in more than one module or presentation, so future work should consider a group-based split using `id_student`.
- The notebook uses only `studentInfo.csv`; registration timing, assessment performance, and learning-platform activity are not included.
- Model hyperparameters were not optimized through cross-validation.
- Probabilities were not calibrated and the classification threshold was not tuned to institutional outreach capacity.
- Missing `imd_band` values were not modeled with an explicit imputation or missing-value indicator.
- The OULAD records cover selected Open University courses from 2013 and 2014, which limits generalization to other institutions and current student populations.

## Future improvements

- Use group-aware cross-validation to keep each student in only one data partition.
- Add registration, assessment, and virtual-learning-environment activity features.
- Compare results across course presentations and test performance over time.
- Tune the decision threshold based on intervention capacity and costs.
- Calibrate predicted probabilities.
- Expand fairness evaluation and monitor performance drift.

## Repository structure

```text
university-dropout-prediction/
├── README.md
├── data/
│   ├── README.md
│   └── studentInfo.csv
├── notebooks/
│   └── dropout_prediction.ipynb
├── presentation/
│   └── dropout_prediction_presentation.pptx
├── images/
│   ├── model_comparison.png
│   └── logistic_regression_confusion_matrix.png
├── requirements.txt
└── LICENSE
```

## Tools

- Python
- pandas and NumPy
- scikit-learn
- XGBoost
- Matplotlib
- Jupyter Notebook

## Data source

Kuzilek, J., Hlosta, M., & Zdrahal, Z. (2017). Open University Learning Analytics dataset. *Scientific Data, 4*, Article 170171. https://doi.org/10.1038/sdata.2017.171

Dataset: https://analyse.kmi.open.ac.uk/open_dataset

## Author

Teresa Ferrill  
Bellevue University, DSC 630 Predictive Analytics
