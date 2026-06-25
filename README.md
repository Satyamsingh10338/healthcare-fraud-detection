# Healthcare  Fraud Detection
 ML based provider risk intelligence system on 558K Medicare claims

# Business Problem
Healthcare fraud costs the US insurance system approximately $300 billion 
annually. Fraudulent providers bill for services never rendered, admit 
patients unnecessarily to collect higher reimbursements, and sometimes 
bill using deceased patient IDs.
Insurance companies receive millions of claims every month across thousands 
of providers. Manual investigation of every provider is impossible. 

This project builds an automated **Provider Risk Intelligence System** that:
- Analyzes the complete billing history of every provider
- Assigns a fraud risk score between 0 and 1
- Explains exactly why each provider was flagged
- Delivers a prioritized audit list for investigation teams
- 
# Project Highlights
   Metric | Value 

 Total Claims Analyzed | 558,211 
 Total Providers | 5,410 
 Features Engineered | 28 
 Model AUC-ROC | 0.97 
 Critical Risk Providers Identified | 83 
 Investigation Scope Reduction | 98% 

## Dataset

Source: [Kaggle — Healthcare Provider Fraud Detection Analysis](https://www.kaggle.com/datasets/rohitrox/healthcare-provider-fraud-detection-analysis)

 File | Rows | Description 

Train_Beneficiarydata | 138,556 | Patient demographics and chronic conditions 
Train_Inpatientdata | 40,474 | Hospital admission claims 
Train_Outpatientdata | 517,737 | Outpatient visit claims 
Train (labels) | 5,410 | Provider fraud labels 

# Technical Approach

 Phase 1 — Data Engineering
- Loaded 4 raw tables with different structures and column counts
- Joined all tables into a unified master claims table (558K rows)
- Handled column conflicts, duplicate merges, and null values
- Made business-justified decisions for every null column
- Converted date columns and engineered time-based features

Phase 2 — Feature Engineering

Transformed 558K claim-level rows into 5,410 provider-level 
behavioral profiles across 5 feature categories:

Category | Features Engineered 

 Volume | total_claims, unique_patients, unique_physicians, inpatient_claims 
 Financial | avg_claim_amount, total_claim_amount, max_claim_amount, avg_deductible 
 Clinical | avg_hospital_stay, max_hospital_stay, avg_claim_duration 
 Diagnosis | unique_diagnosis_1/2/3, diagnosis_diversity 
 Patient Profile | avg_patient_age, deceased_patient_rate, chronic condition rates 
 Derived Ratios | claims_per_patient, claims_per_physician, inpatient_ratio 

Phase 3 — Modeling and Evaluation

Class Imbalance: Only 9.4% of providers are fraudulent.
Handled using scale_pos_weight in XGBoost and class_weight in 
Logistic Regression. Accuracy rejected as a metric — AUC-ROC 
and Recall used as primary evaluation metrics.

  Metric | Logistic Regression (Baseline) | XGBoost 

 AUC-ROC | 0.9669 | 0.9675 
 Recall | 0.9010 | 0.7327 
 Precision | 0.4600 | 0.6167 
 F1 Score | 0.6100 | 0.6697 

Key insight: Baseline already achieved 0.97 AUC-ROC — 
validating that feature engineering was the primary value driver. 
XGBoost improved Precision by 34%, reducing false investigation 
alerts from 107 to 46 providers.

Phase 4 — SHAP Explainability

Applied SHAP TreeExplainer to make every prediction fully interpretable.
The system does not just output a risk score,it explains exactly 
which features drove each provider's fraud probability.

Top Global Fraud Drivers:

 Rank | Feature | SHAP Importance 

 total_claim_amount | 0.281 
 max_hospital_stay | 0.130 
 claims_per_patient | 0.068 
 unique_diagnosis_3 | 0.039 
 max_claim_amount | 0.038 

Individual Provider Example:
Provider PRV52985 — Fraud Probability: 99.9%
- total_claim_amount $1.72M → contributed +2.49 to fraud score
- max_hospital_stay 35 days → contributed +1.10 to fraud score  
- low diagnosis diversity → contributed +0.59 to fraud score
- These three factors alone explain 83% of the fraud risk

Phase 5 — Business Output

Providers stratified into 4 risk tiers:

Risk Category | Count | Recommended Action 

 Critical | 83 | Investigate immediately 
 High | 25 | Investigate within 30 days 
 Medium | 46 | Monitor closely 
 Low | 928 | No action required 

Business Impact: Reduces investigation scope from 5,410 providers 
to 83 critical cases a 98% reduction while providing a specific 
explanation for every flagged provider.


#SHAP Visualizations

Global Feature Importance


<img width="1198" height="1111" alt="shap_waterfall_plot" src="https://github.com/user-attachments/assets/eaf5f892-9bf9-4019-bffe-490e1b6c91d6" />


Feature Impact Summary


<img width="1161" height="1110" alt="shap_summary_plot" src="https://github.com/user-attachments/assets/1a73857c-2c2d-48d3-90c1-d13662b75a26" />


Individual Provider Explanation


<img width="1185" height="1111" alt="shap_bar_plot" src="https://github.com/user-attachments/assets/37f218e0-f9d8-4fe7-975c-cbffe3ac8ad6" />


#Power BI Dashboard

3-page executive dashboard built on model outputs:



Page 1 — Executive Overview
KPI cards, risk distribution donut chart, top critical providers 
bar chart, audit priority table sorted by fraud probability



<img width="1167" height="658" alt="Executive Overview" src="https://github.com/user-attachments/assets/769228f0-44d5-4169-ad7a-2e769b240a24" />




Page 2 — Financial Analysis
Fraud probability vs claim amount scatter plot, average claim 
amount by risk tier, top fraud drivers distribution



<img width="1172" height="671" alt="Financial Analysis" src="https://github.com/user-attachments/assets/ad031ee0-5155-4a53-a95c-f63261d7c96a" />




Page 3 — Clinical Behavior
Inpatient ratio by risk tier, diagnosis diversity comparison, 
claims per patient analysis, hospital stay patterns



<img width="1157" height="656" alt="Clinical Behavior" src="https://github.com/user-attachments/assets/e272df7b-0eae-48a7-bffc-4ea389a116b0" />





