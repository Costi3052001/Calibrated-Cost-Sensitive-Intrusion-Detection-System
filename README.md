# Intrusion Detection with Calibrated Logistic Regression on UNSW-NB15

This project builds a **binary intrusion detection model** using the **UNSW-NB15 dataset**. The notebook trains a **cost-sensitive, probability-calibrated Logistic Regression classifier** to distinguish between:

- **0 = Normal traffic**
- **1 = Attack traffic**

The implementation includes preprocessing, model training, probability calibration, cost-sensitive threshold selection, evaluation metrics, and interpretability through feature coefficients.

---

## Project Overview

The goal of this project is to detect malicious network traffic using supervised machine learning.

The workflow includes:

- Loading the UNSW-NB15 datasets
- Cleaning and aligning features
- Encoding categorical variables
- Scaling numerical features
- Training a Logistic Regression model
- Applying probability calibration with sigmoid scaling
- Selecting an operating threshold based on false positive / false negative costs
- Evaluating performance using ROC AUC, Average Precision, confusion matrix, and plots


---

## Dataset

This project uses the following files:

- `UNSW_NB15_set.csv`


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
- `attack_cat` 

### Categorical Features

The following columns are treated as categorical and one-hot encoded when available:

- `proto`
- `service`
- `state`

### Numerical Features

All remaining columns are treated as numeric features, converted to numeric format.
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
- **Confusion Matrix**
- **ROC Curve**
- **Precision-Recall Curve**
- **Total Cost vs Threshold Curve**


### Probability-Based Metrics (Initial Results)

- **ROC AUC:** `0.9746`
- **Average Precision:** `0.9870`
- **Brier Score:** `0.0704`

### Best Cost-Sensitive Threshold (Initial Results)

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


## Installation

Install the required Python libraries with:

```bash
pip install -U scikit-learn pandas matplotlib
```

---

