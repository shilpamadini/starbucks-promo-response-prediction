# Starbucks Promotional Response Prediction Using Machine Learning

This project analyzes customer behavior in the Starbucks Rewards mobile app using a simulated dataset provided by Udacity. The goal is to predict whether a customer will respond positively to a promotional offer—taking into account user demographics, offer characteristics, and behavioral event logs.
This work is completed as part of the Udacity Machine Learning Engineer Nanodegree Capstone Project.

## Project Objective

Starbucks sends various promotional offers (BOGO, discounts, informational). Customers respond differently—some view and complete offers, some ignore them, and some purchase without any offers.
Build a machine learning model that predicts offer responsiveness for each customer–offer pair to support personalized marketing strategies.


## Dataset Overview

The dataset consists of three files:

1. portfolio.json

Offer metadata (type, difficulty, reward, duration, channels)

2. profile.json

Customer demographics (age, gender, income, membership date)

3. transcript.json

Event logs: offer received, offer viewed, offer completed, transaction events

Although simulated, the data is constructed to mimic realistic behavioral patterns, such as:

Customers completing offers without viewing them

Purchases not influenced by offers

Customers who respond negatively or ignore offers

## Data Processing Steps

Merge customer, offer, and event logs

Flatten transcript value fields

Construct offer validity windows

Define "responded" label as viewed + completed within window

Engineer features such as tenure, age buckets, income bands, and delivery channels

## Machine Learning Approach
Models Used

Logistic Regression (baseline)

Random Forest / XGBoost

AutoGluon Tabular (optional)

## Evaluation Metrics

ROC-AUC (primary)

Precision, Recall

F1-score

Confusion matrix (important for false positives / unnecessary offers)

## Business Insights

Identify which segments respond best to which offers

Identify negative responders (users who should not receive offers)

Support targeted marketing strategy decisions
