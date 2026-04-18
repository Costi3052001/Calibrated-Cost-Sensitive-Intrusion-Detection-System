# Intrusion Detection with Calibrated Logistic Regression on UNSW-NB15

This project builds a **binary intrusion detection model** using the **UNSW-NB15 dataset**. The notebook trains a **cost-sensitive, probability-calibrated Logistic Regression classifier** to distinguish between:

- **0 = Normal traffic**
- **1 = Attack traffic**

The implementation includes preprocessing, model training, probability calibration, cost-sensitive threshold selection, evaluation metrics, and interpretability through feature coefficients.

---

## Project Overview

The goal of this project is to detect malicious network traffic using supervised machine learning.

The workflow includes:

- Loading the UNSW-NB15 training and testing datasets
- Cleaning and aligning features
- Encoding categorical variables
- Scaling numerical features
- Training a Logistic Regression model
- Applying probability calibration with sigmoid scaling
- Selecting an operating threshold based on asymmetric false positive / false negative costs
- Evaluating performance using ROC AUC, Average Precision, Brier score, confusion matrix, and plots
- Inspecting the most influential model coefficients

---

## Dataset

This project uses the following files:

- `UNSW_NB15_training-set.csv`
- `UNSW_NB15_testing-set.csv`

Both files must be placed in the same working directory as the notebook before running the code.

The target column is:

- `label`

Optional metadata column handled by the notebook:

- `attack_cat`

---

## Features Used

The notebook removes identifier and timestamp-related fields that are less useful for generalizable prediction:

- `id`
- `srcip`
- `dstip`
- `sport`
- `dsport`
- `stime`
- `ltime`
- `label`
- `attack_cat` (if present)

### Categorical Features

The following columns are treated as categorical and one-hot encoded when available:

- `proto`
- `service`
- `state`

### Numerical Features

All remaining columns are treated as numeric features, converted to numeric format, and missing values are filled using training-set medians.

---

## Model Pipeline

The model pipeline consists of:

1. **StandardScaler** for numerical features
2. **OneHotEncoder** for categorical features
3. **LogisticRegression** classifier with:
   - `solver='saga'`
   - `penalty='l2'`
   - `class_weight='balanced'`
   - `max_iter=3000`
   - `random_state=42`

The full pipeline is then calibrated using:

- `CalibratedClassifierCV(method="sigmoid", cv=3)`

This improves the reliability of predicted probabilities.

---

## Cost-Sensitive Decision Threshold

Instead of using the default classification threshold of `0.5`, this project searches for the threshold that minimizes total classification cost on the test set.

The cost setup used is:

- **False Positive Cost (C_FP) = 1**
- **False Negative Cost (C_FN) = 5**

This makes false negatives more expensive than false positives, which is appropriate in intrusion detection where missed attacks are usually more critical.

---

## Evaluation Metrics

The notebook evaluates the model using:

- **ROC AUC**
- **Average Precision**
- **Brier Score**
- **Confusion Matrix**
- **ROC Curve**
- **Precision-Recall Curve**
- **Calibration Curve**
- **Total Cost vs Threshold Curve**

---

## Results

### Dataset Size

- **Training set:** `(82332, 45)`
- **Testing set:** `(175341, 45)`

### Processed Features

- **Total aligned features:** `42`
- **Categorical columns:** `['proto', 'service', 'state']`
- **Numeric columns:** `39`

### Probability-Based Metrics

- **ROC AUC:** `0.9746`
- **Average Precision:** `0.9870`
- **Brier Score:** `0.0704`

### Best Cost-Sensitive Threshold

- **C_FP = 1.0**
- **C_FN = 5.0**
- **Best threshold:** `0.216`
- **Minimum total cost:** `20873.0`

### Confusion Matrix at Best Threshold

```text
[[ 44622  11378]
 [  1899 117442]]
```

Where:

- **TN = 44622**
- **FP = 11378**
- **FN = 1899**
- **TP = 117442**

---

## Repository Structure

```text
.
├── Project_Implementation1.ipynb
├── UNSW_NB15_training-set.csv
├── UNSW_NB15_testing-set.csv
└── README.md
```

---

## Installation

Install the required Python libraries with:

```bash
pip install -U scikit-learn pandas matplotlib
```

---

## How to Run

1. Place the dataset files in the same folder as the notebook:
   - `UNSW_NB15_training-set.csv`
   - `UNSW_NB15_testing-set.csv`

2. Open the notebook:

```bash
jupyter notebook Project_Implementation1.ipynb
```

3. Run all cells.

The notebook will:

- load and validate the dataset files
- preprocess the data
- train and calibrate the classifier
- compute evaluation metrics
- search for the optimal cost-sensitive threshold
- display plots and confusion matrix
- print top positive and negative coefficients for interpretation

---

## Interpretability

The notebook extracts Logistic Regression coefficients after training and calibration to show:

- features with the strongest positive contribution toward **attack**
- features with the strongest negative contribution toward **normal traffic**

This helps make the model more explainable compared to black-box approaches.

---

## Why Logistic Regression?

Logistic Regression was chosen because it is:

- simple and efficient
- easy to interpret
- suitable for large tabular datasets
- capable of producing probabilities
- improved further through calibration

Even though more complex models may achieve slightly higher raw accuracy, this approach provides a strong balance between:

- performance
- interpretability
- probability quality
- operational decision-making

---

## Future Improvements

Possible extensions to this project include:

- comparing Logistic Regression with tree-based models such as Random Forest or XGBoost
- performing hyperparameter tuning
- using cross-validation for threshold selection
- handling class imbalance with additional sampling methods
- adding feature selection or dimensionality reduction
- evaluating per-attack-category performance
- exporting the trained pipeline for deployment

---

## Conclusion

This project demonstrates a practical machine learning approach to network intrusion detection using the UNSW-NB15 dataset.

By combining:

- structured preprocessing
- calibrated probability estimates
- cost-sensitive thresholding
- clear evaluation metrics
- model interpretability

the implementation provides a strong and practical baseline for cybersecurity classification tasks.

---

## Author

Project implementation for intrusion detection using machine learning on the UNSW-NB15 dataset.
