# Bank Marketing Campaign Response Prediction

## IT3091 Machine Learning Group Assignment

A machine learning project based on the **Bank Marketing** dataset. The project investigates how a bank can use customer information to improve the effectiveness of a marketing campaign by predicting which customers are more likely to subscribe to a term deposit and using those predictions to prioritise outbound calls.

### Project Information

| Item           | Details                         |
| -------------- | ------------------------------- |
| Module         | IT3091 Machine Learning         |
| Track          | Guided Data Track               |
| Domain         | Banking & Finance               |
| Dataset        | Bank Marketing                  |
| Primary Lens   | Campaign Response Prediction    |
| Secondary Lens | Customer Segment Prioritisation |
| Task           | Binary Classification           |
| Target         | `y` (term deposit subscription) |
| Final Model    | Random Forest                   |

---

## 1. Project Objective

The main question addressed by this project is:

> **Which customers should be prioritised for an outbound marketing call when the bank has limited calling capacity?**

The project uses customer demographic, financial, and campaign-history information to predict the probability that a customer will subscribe to a term deposit.

The predicted probabilities can then be used to rank customers and create priority groups for the marketing team.

### Business Lenses

**Primary lens: Campaign Response Prediction**

We use supervised binary classification to predict whether a customer will subscribe to the term deposit.

**Secondary lens: Customer Segment Prioritisation**

The predicted subscription probabilities are used to rank customers and identify higher-priority groups for calling. This is a direct use of the primary model output rather than a separate analysis.

---

## 2. Dataset

The project uses the **Bank Marketing dataset** containing **45,211 customer records** and 16 input variables before preprocessing.

The target variable is:

* `y = yes`: customer subscribed to the term deposit
* `y = no`: customer did not subscribe

The dataset has a strong class imbalance:

* No: approximately **88.3%**
* Yes: approximately **11.7%**

The dataset was obtained through the UCI Machine Learning Repository.

### Important Data Decisions

Several data-quality decisions were made during the project:

* Genuine unknown values in `job`, `education`, and `contact` were represented using an explicit `unknown` category.
* Structural missingness in `poutcome` was represented as `no_prior_campaign`.
* `duration` was removed because it is only known after a call takes place and would therefore cause data leakage.
* `previously_contacted` was engineered from `pdays`.
* `pdays_capped` was removed after the scaling issue and redundancy with other contact-history variables were investigated.
* Categorical variables were one-hot encoded.
* Numeric variables were scaled using `RobustScaler`.
* A stratified 80/20 train/test split was used.

---

## 3. Machine Learning Workflow

The project followed the workflow below:

```text
Business Problem
       ↓
Problem Framing & Lens Selection
       ↓
Data Understanding & EDA
       ↓
Data Quality Checks
       ↓
Preprocessing & Feature Engineering
       ↓
Train/Test Split
       ↓
Baseline Model
       ↓
Model Comparison
       ↓
Feature Investigation
       ↓
Evaluation & Threshold Analysis
       ↓
Bootstrap Validation
       ↓
Business Recommendation
```

The workflow included several decision points rather than following a fixed modelling recipe. For example, suspicious feature importance led to further investigation of `contact_unknown`, while disagreement between PR-AUC and threshold-specific business value led to additional bootstrap validation.

---

## 4. Models Compared

Four classification models were developed and compared:

| Model               | Role          |
| ------------------- | ------------- |
| Logistic Regression | Baseline      |
| Decision Tree       | Alternative 1 |
| Random Forest       | Alternative 2 |
| XGBoost             | Alternative 3 |

The models were compared using several measures rather than accuracy alone, including:

* Precision
* Recall
* F1-score
* ROC-AUC
* PR-AUC
* Lift
* Threshold-specific business value

The model comparison was also used to investigate whether apparently useful features represented genuine predictive information or possible data-quality issues.

---

## 5. Important Model Investigation

During model comparison, `contact_unknown` showed unexpectedly strong predictive behaviour.

The group investigated this feature using:

1. Cross-tabulation with campaign month
2. Additional data investigation
3. Ablation testing

The investigation showed that `contact_unknown` was strongly associated with particular campaign months and could act as a proxy for campaign timing rather than representing genuine customer information.

Based on this evidence, the feature was removed from the final feature set.

This was an example of using model results to identify a data issue rather than simply accepting feature importance at face value.

---

## 6. Evaluation and Model Selection

The project initially indicated strong performance from XGBoost based on global ranking metrics.

However, the group also evaluated the models using an illustrative business-value framework:

* Cost per call: **$5**
* Value per successful subscription: **$80**

These values were used only as illustrative assumptions because actual bank cost and value figures were not available.

Threshold analysis showed that the model with the strongest global ranking metric was not necessarily the best model at the specific operating threshold relevant to the business decision.

Bootstrap validation was then used to investigate the difference between XGBoost and Random Forest.

Based on this analysis, the final model selection was revised from **XGBoost to Random Forest**.

---

## 7. Final Recommendation

The final recommendation is to use **Random Forest subscription probabilities** to create a prioritised customer calling list.

Under the illustrative $5/$80 cost-value scenario, an example operating threshold of approximately **0.35** produced the following result on the test set:

* Approximately **81.0% of actual subscribers** were identified.
* Approximately **48.9% of the test-set customers** would be contacted.

The model can also be used as a ranking system. If the bank has a fixed call-centre capacity, it can contact the **top-N highest-scored customers** rather than using a fixed probability threshold.

### Priority Groups

The predicted probabilities can be used to organise customers into priority levels such as:

* **High priority:** highest predicted subscription probability
* **Medium priority:** moderate predicted probability
* **Low priority:** lower predicted probability

The exact threshold or number of customers contacted should be recalculated using the bank's actual campaign costs, expected subscription value, and available call-centre capacity.

---

## 8. Business Value

The analysis provides several potential benefits:

* Reduces unnecessary calls to lower-propensity customers.
* Helps marketing staff prioritise customers when calling capacity is limited.
* Improves the concentration of potential subscribers within the highest-ranked customer groups.
* Provides a data-driven approach to campaign targeting instead of treating all customers equally.

Calling only the highest-scored customers produced a substantially higher subscription rate than the overall dataset base rate.

These results should be interpreted as findings from the historical dataset rather than guaranteed results for a future campaign.

---

## 9. Limitations

This project was completed as a university machine learning project using a public historical dataset. Therefore, the results should not be treated as a ready-to-use banking system.

Important limitations include:

* The dataset represents historical campaign activity and may not represent current customer behaviour.
* The cost and subscription-value figures used for threshold analysis are illustrative.
* Actual call-centre capacity was not available.
* A formal fairness audit was not performed.
* Model and data drift could affect future performance.
* The model requires validation using current bank data before real deployment.

The model is intended as **decision support**, rather than a fully automated system that makes customer-contact decisions without human review.

---

## 10. Reproducibility

The project was developed using Python and common machine learning/data analysis libraries.

Main tools and libraries include:

* Python
* Pandas
* NumPy
* Scikit-learn
* XGBoost
* Matplotlib
* Jupyter Notebook / Google Colab

The repository contains the project notebook/code, preprocessing outputs, documentation, and supporting project materials.

### Repository Structure

```text
Bank-Marketing-ML/
│
├── Data/
│   └── PreprocessedData/
│
├── Notebooks/
│   └── Final_Notebook.ipynb
│
├── Documentation/
│   ├── Problem_Framing/
│   ├── EDA_Log/
│   ├── Preprocessing_Log/
│   ├── Model_Comparison_Log/
│   ├── Evaluation_Log/
│   └── Decision_Log/
│
├── Report/
│   └── Final_Report.pdf
│
└── README.md
```

> Update the folder names above if the actual GitHub repository uses different names.

---

## 11. Project Documentation

The repository is organised around the evidence required for the IT3091 Machine Learning assignment.

The main supporting documents include:

* Problem Framing Canvas
* Workflow Diagram
* Data Dictionary
* EDA Insight Log
* Preprocessing and Feature Engineering Log
* Model Comparison Log
* Evaluation Log
* Decision Log
* Final Report
* Final Notebook

---

## 12. Team Contributions

The project was completed as a group, with members taking primary responsibility for different areas:

| Student ID | Role                                           |
| ---------- | ---------------------------------------------- |
| IT24103178 | Modeling Lead                                  |
| IT24102904 | Evaluation Lead                                |
| IT24103159 | Framing & Data Preparation Lead                |
| IT24102479 | Data Understanding & Business Translation Lead |

All members also contributed to project discussions, workflow and decision documentation, reproducibility, report preparation, and final review.

---

## 13. AI Use

AI tools were used as development assistants during the project.

Claude (Anthropic) was used to support tasks such as:

* Suggesting preprocessing and modelling approaches.
* Explaining modelling trade-offs.
* Supporting the design and interpretation of statistical validation.
* Helping diagnose technical issues.
* Generating boilerplate code based on decisions made by the team.
* Supporting report and documentation structure.

The team retained responsibility for the project's analytical decisions. Model selection, preprocessing decisions, feature investigations, evaluation, and final recommendations were reviewed and approved by the team using the project's own data and results.

---

## 14. Key Takeaway

This project demonstrates how machine learning can be used not only to build a prediction model, but also to support a practical marketing decision.

The main outcome is a **Random Forest-based customer prioritisation approach** that uses predicted subscription probabilities to help determine which customers should be contacted first.

The final recommendation is conditional on the historical data and illustrative business assumptions. Actual deployment would require current customer data, real campaign economics, capacity information, fairness assessment, and ongoing model monitoring.

---

## Team

**IT3091 Machine Learning | Guided Data Track | Banking & Finance**

Academic project completed using the Bank Marketing dataset.
