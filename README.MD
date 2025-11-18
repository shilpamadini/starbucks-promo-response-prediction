# Starbucks Promotional Response Prediction

Starbucks sends multiple types of promotions (BOGO, discount, informational) to customers.However, Not all customers view or complete the offers.Some customers buy regardless of offers.Some customers respond negatively and prefer no offer.Some complete offers without viewing, meaning they were not influenced.This project analyzes a simulated dataset that mimics customer behavior on the Starbucks Rewards mobile app and builds a machine learning model to predict whether a customer will respond to a specific offer.
This enables data-driven marketing strategies and avoids sending promotions that cost money but do not change behavior.

## Dataset Overview

All data comes from the Starbucks Capstone dataset (simulated for research).
Files:

1. portfolio.json — Offer Metadata

offer ID

offer type (bogo, discount, informational)

difficulty (spend threshold)

reward amount

duration

delivery channels

2. profile.json — Customer Demographics

customer ID

age

gender

income

became_member_on

3. transcript.json — Event Logs

Events include:

offer received

offer viewed

offer completed

transaction

Each with timestamps and associated metadata.

## Project Workflow 
0. Workspace Setup

Create SageMaker Studio environment

Clone this GitHub repo

Dynamically obtain execution role and default S3 bucket

Create project folder structure in S3:
```
s3://<bucket>/starbucks/data/
s3://<bucket>/starbucks/model/
```
1. Upload Raw Data to S3

The notebook loads JSON files from the repository and uploads them to S3:
```
s3://<bucket>/starbucks/data/portfolio.json
s3://<bucket>/starbucks/data/profile.json
s3://<bucket>/starbucks/data/transcript.json
```
2. Load Data From S3

The notebook reads all three Starbucks JSON files from S3 into pandas DataFrames using:

boto3

pandas.read_json(io.BytesIO())

3. Exploratory Data Analysis (EDA)

Performed in the notebook:

Demographics summary (age, income, gender)

Offer metadata overview

Transcript events distribution

Event sequencing patterns

Missing values exploration

Offer windows and timestamps

Purchase behavior without offers

4. Offer Response Labeling (Critical Step)

The notebook implements strict causal labeling logic:

A customer responded to an offer ONLY IF:

They viewed the offer within its validity window

They completed the offer within the same window

Also identified:

Customers who complete offers without viewing → not influenced

Customers who purchase without offers → baseline buyers

Customers who respond negatively (view but do not act)

A final labeled dataset is produced.

5. Feature Engineering

Features used in modeling include:

Customer features:

age

income

gender

membership tenure (member_days)

Offer features:

difficulty

reward

duration

offer_type (encoded)

Channel features (one-hot):

email

web

mobile

social

This results in a clean modeling dataset.

6. Train / Validation / Test Split

The notebook splits the data into:

60% training

20% validation

20% testing

All three files are saved to S3 in:
```
s3://<bucket>/starbucks/data/train.csv  
s3://<bucket>/starbucks/data/val.csv  
s3://<bucket>/starbucks/data/test.csv  
```
7. Baseline Model – Logistic Regression

Using:

sklearn pipeline

numerical + categorical imputation

one-hot encoding

logistic regression classifier

Results:

Accuracy: ~0.75

ROC AUC: ~0.84

8. Advanced Model – Random Forest

RandomForestClassifier significantly improves performance:

Results:

Accuracy: ~0.78

ROC AUC: ~0.86

9. AutoGluon Tabular – Best Model

AutoGluon was used for automated model selection & ensembling:
```
predictor = TabularPredictor(...).fit(
    train_data,
    tuning_data=val_data,
    presets="best_quality",
    use_bag_holdout=True,
    time_limit=1800
)
```

AutoGluon automatically trains dozens of:

LightGBM models

CatBoost

XGBoost

RandomForest

Neural networks

Stacked ensembles

Best Model Identified:
WeightedEnsemble_L3

Final Test ROC AUC:

0.8818 — Highest among all models
10. Final Model Inference

The notebook demonstrates real inference:

Load test samples

Run predictor.predict()

Run probability scoring with predictor.predict_proba()

Produce actionable offer recommendations for customers

This simulates a real marketing decision engine.

10. Model Saving (Optional)

AutoGluon model folders are large. Saving the entire model directory to S3 was optional and not required for this capstone, so it was skipped to avoid long upload times.

Results Summary

| Model                        | Accuracy | ROC AUC |
|------------------------------|----------|---------|
| Logistic Regression          | 0.75     | 0.839   |
| Random Forest                | 0.78     | 0.858   |
| **AutoGluon (Best Ensemble)** | **0.80** | **0.882** |


AutoGluon outperformed all manual models with strong ranking performance.

## Key Insights

Customers with higher income and longer membership respond more positively.

Discount offers generally outperform BOGO for most demographics.

Some users purchase regardless of promotions → sending offers to them wastes budget.

Some users respond negatively → best strategy is no offer.

Channel delivery matters — mobile + web yielded the strongest engagement patterns.

## Technology Stack

Python 3.12

AWS SageMaker Studio

AWS S3

Pandas, NumPy, Matplotlib, Seaborn

Scikit-learn

AutoGluon Tabular

Boto3
