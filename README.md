# Loan Eligibility Prediction

## Overview

In today's rapidly evolving financial landscape, the demand for loans
has increased significantly as individuals and businesses rely on credit
to meet personal, educational, housing, and entrepreneurial needs.
Financial institutions receive a large volume of loan applications
daily, making it challenging to evaluate each applicant quickly while
maintaining accuracy and fairness.

As a result, there is a growing need for intelligent tools that can
assist in the preliminary screening of loan applicants.

The **Quick Loan Eligibility Predictor** is designed to address this
challenge by providing a fast and data-driven method to estimate whether
a loan applicant is likely to qualify for a loan. Using historical
financial data and key applicant attributes such as income, employment
status, credit history, and property area, the system generates a
prediction that helps financial institutions quickly assess the
potential eligibility of an applicant.

------------------------------------------------------------------------

# Goal and Audience

The goal of this project is to build a predictor that can classify loan
approval status (`Loan_Status`).

Financial services companies could benefit from utilizing this predictor
to:

-   Quickly screen loan applications
-   Reduce the number of applications requiring manual review
-   Improve efficiency in the approval pipeline

------------------------------------------------------------------------

# Data Source

The dataset used for this project was sourced from **Kaggle** and
contains the following fields:

  -----------------------------------------------------------------------
  Column                  Description             Example
  ----------------------- ----------------------- -----------------------
  Customer_ID             Unique identifier for   569
                          each loan applicant     

  Gender                  Gender of the applicant Male / Female

  Married                 Marital status of the   Yes / No
                          applicant               

  Dependents              Number of dependents    0, 1, 2, 3

  Education               Education level of the  Graduate / Not Graduate
                          applicant               

  Self_Employed           Whether the applicant   Yes / No
                          is self-employed        

  Applicant_Income        Applicant's monthly     5000
                          income                  

  Coapplicant_Income      Coapplicant's monthly   1500
                          income                  

  Loan_Amount             Loan amount requested   128
                          (in thousands)          

  Loan_Amount_Term        Loan repayment term     360
                          (months)                

  Credit_History          Credit history meets    1
                          lending criteria (1 =   
                          Yes, 0 = No)            

  Property_Area           Type of property area   Urban / Semiurban /
                                                  Rural

  Loan_Status             Loan approved or not    Y / N
                          (target variable)       
  -----------------------------------------------------------------------

The **`Loan_Status`** column is the dependent variable the model
attempts to predict.

------------------------------------------------------------------------

# Data Wrangling

## I. Feature Engineering

The original dataset was already in relatively good condition and
required minimal cleaning.

However, several additional features were engineered to better reflect
financial factors commonly used during loan application screening:

-   **Total_Income**
    -   `Applicant_Income + Coapplicant_Income`
-   **Monthly_Payment**
    -   `Full_Loan_Amount (in thousands) / Loan_Amount_Term`
-   **Debt_To_Income_Ratio**
    -   `Monthly_Payment / Total_Income`

These additional features help provide stronger predictive signals for
the models.

------------------------------------------------------------------------

## II. Data Cleaning and Processing

The following preprocessing steps were performed:

-   **One-hot encoding** for categorical features such as
    `Property_Area`
-   **Binary categorical variables mapped to numeric values (0/1)**
    -   Gender
    -   Education
    -   Marital Status
    -   Loan Status
-   **Feature scaling** applied to numeric columns before modeling
-   **Two anomalous records removed** after investigation during
    analysis

------------------------------------------------------------------------

## III. Data Analysis

Initial investigation began by examining distributions across features.

Analysis revealed that **income alone does not determine loan
approval**, which motivated the creation of the **Debt_To_Income_Ratio**
feature.

### Outlier Investigation

Assuming **Debt_To_Income_Ratio** and **Credit_History** are strong
predictors of loan approval, the data was examined by slicing approval
status.

An anomaly was discovered:

-   **Record ID 498** showed:
    -   Debt-to-income ratio **\> 100%**
    -   **No credit history**
    -   Yet the loan was **approved**

This is unusual because a loan with a monthly payment exceeding total
income would rarely be approved.

Further verification showed:

-   Approved loans **without credit history** typically had\
    **Debt_To_Income_Ratio \< 11%**

Because record **498** violated this pattern, it was classified as **bad
data and removed from the dataset**.

### Correlation Findings

A correlation matrix showed that:

**Credit_History is strongly correlated with Loan_Status**, indicating
it is one of the most influential predictors in the dataset.
