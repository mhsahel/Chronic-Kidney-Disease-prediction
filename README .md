# Chronic Kidney Disease Prediction

A machine learning project that predicts whether a patient has **Chronic Kidney Disease (CKD)** from clinical and lab measurements. It covers the full workflow: data cleaning, exploratory analysis, preprocessing pipelines, model comparison, and final evaluation.

## Results at a Glance

| Metric (test set, 80 patients) | Score |
|---|---|
| Accuracy | **98.75%** |
| F1-score | 0.990 |
| Recall (CKD patients caught) | **1.00** |
| ROC-AUC | 0.983 |

**Confusion matrix (test set)**

|  | Predicted: No CKD | Predicted: CKD |
|---|---|---|
| **Actual: No CKD** | 29 | 1 |
| **Actual: CKD** | 0 | 50 |

The model found every CKD patient in the test set and made only one false alarm. Recall matters most here, because missing a sick patient is worse than flagging a healthy one.

## Dataset

- **Source:** UCI Machine Learning Repository, Chronic Kidney Disease dataset (`chronic_kidney_disease_full.arff`)
- **Size:** 400 patients, 24 features plus 1 target (`class`)
- **Target:** `ckd` (62.5%) vs `notckd` (37.5%)
- **Duplicates:** none
- **Missing values:** heavy in some columns, for example `rbc` (38%), `rbcc` (32.8%), `wbcc` (26.5%), `pot` (22%), `sod` (21.8%)

**Feature groups**

| Type | Columns |
|---|---|
| Numerical | `age`, `bp`, `bgr`, `bu`, `sc`, `sod`, `pot`, `hemo`, `pcv`, `wbcc`, `rbcc` |
| Ordinal | `sg` (specific gravity), `al` (albumin), `su` (sugar) |
| Categorical | `rbc`, `pc`, `pcc`, `ba`, `htn`, `dm`, `cad`, `appet`, `pe`, `ane` |

> Please check the dataset's own license and citation requirements on UCI before reusing it. The license of this repository covers the code only.

## Workflow

1. **Load and clean:** the raw ARFF file has some malformed rows, so it is parsed manually. `?` values are treated as missing.
2. **Data quality checks:** shape, duplicates, missing values, data types, class balance.
3. **Exploratory data analysis:**
   - Box plots of every numerical feature against the class
   - Count plots of every categorical and ordinal feature against the class
   - Correlation heatmap of the numerical features
4. **Train/test split:** 80/20, stratified, `random_state=42` (320 train / 80 test).
5. **Preprocessing with scikit-learn pipelines** (fit on the training data only, to avoid leakage):
   - Numerical: median imputation, then standard scaling
   - Categorical: most-frequent imputation, then one-hot encoding
   - Ordinal: most-frequent imputation, ordinal encoding with a fixed level order, then scaling
6. **Model comparison** with 5-fold cross-validation.
7. **Final evaluation** on the held-out test set, plus a train vs test comparison to check for overfitting.

## Key Findings from EDA

- Hemoglobin (`hemo`), packed cell volume (`pcv`) and red blood cell count (`rbcc`) are strongly correlated with each other (about 0.9 for `hemo`/`pcv` and about 0.8 for `hemo`/`rbcc`).
- Patients without CKD generally have no hypertension (`htn`) or diabetes (`dm`).
- `age`, `sc`, `bu` and `bgr` contain a notable number of outliers.

## Model Comparison (5-fold CV on training data)

| Model | Accuracy | F1 | Recall | ROC-AUC |
|---|---|---|---|---|
| Logistic Regression | 0.99 | 0.99 | 0.99 | 1.00 |
| K-Nearest Neighbors (k=5) | 0.97 | 0.97 | 0.94 | 0.99 |
| Random Forest (500 trees) | 0.99 | 0.99 | 0.99 | 1.00 |

Logistic Regression and Random Forest scored the same, and **Random Forest** was chosen as the final model (`n_estimators=500`, `max_depth=3`).

**Overfitting check:** train accuracy 0.994 vs test accuracy 0.988, so the gap is small.

## Tech Stack

- Python 3
- pandas, NumPy
- scikit-learn
- Matplotlib, Seaborn
- Google Colab / Jupyter Notebook



If you run it on Google Colab, upload the `.arff` file and keep the path used in the first cell (`/content/chronic_kidney_disease_full.arff`).

## Visualizations

| Box plots vs class | Count plots vs class | Correlation heatmap |
|---|---|---|
| ![Box plots](images/box_plots.png) | ![Count plots](images/count_plots.png) | ![Heatmap](images/heatmap.png) |

## Limitations

- The dataset is small (400 patients, 80 in the test set), so a single misclassification moves the metrics noticeably.
- Several columns have a lot of missing values, which were filled by simple imputation.
- This is a learning and portfolio project. It is **not a medical tool** and must not be used for real diagnosis.

## Future Improvements

- Hyperparameter tuning with `GridSearchCV` or `RandomizedSearchCV`
- Feature importance analysis and model explainability (for example SHAP)
- Try other models such as Gradient Boosting or XGBoost
- Wrap the model in a small web app (Streamlit or Flask)

## Author

**Sahel**, student at AIUB, aspiring AI engineer.

- GitHub: [your-username](https://github.com/your-username)
- LinkedIn: [your-profile](https://www.linkedin.com/in/your-profile)

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
