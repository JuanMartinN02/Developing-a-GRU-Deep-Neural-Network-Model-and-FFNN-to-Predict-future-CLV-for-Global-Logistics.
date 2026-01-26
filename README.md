# Customer Lifetime Value Prediction with Neural Networks

This repository contains the full implementation of a Customer Lifetime Value (CLV) prediction system built using real transactional data. The project covers the complete pipeline, from raw data preprocessing to model training and deployment through a REST API.

The main objective is to study how different neural network architectures perform when predicting future customer spending under realistic data conditions, including noise, missing activity periods, heavy-tailed distributions, and heterogeneous customer behavior.

The project was developed as part of an undergraduate thesis in Systems Engineering, with a strong focus on applied machine learning and real-world constraints.

## Project overview

The repository includes:

 -A Feed Forward Neural Network (FFNN) trained on aggregated customer features.

 -A Gated Recurrent Unit (GRU) model trained on sequential monthly transaction data.

 -A FastAPI application that loads a trained GRU model and produces CLV predictions from new transactional CSV files.

The GRU model is the main model used in production, as it consistently outperformed the non-sequential approaches.

## Data characteristics:

The models were trained using a public transactional dataset with properties similar to enterprise-grade logistics and retail systems:

 -Millions of transaction records

 -Irregular purchase behavior

 -Long inactivity periods

 -Highly skewed monetary distributions

 -Strong presence of outliers (e.g. wholesale customers)

This makes the problem closer to a real production scenario than to a clean academic dataset.

## Models:

### Feed Forward Neural Network (FFNN):

The FFNN uses customer-level aggregated features derived from historical transactions, such as:

 -Total monetary value

 -Purchase frequency

 -Recency

 -Product diversity

 -Activity metrics

 -Short-term spending and frequency indicators

This model serves as a strong baseline but does not explicitly model temporal dependencies.


### Gated Recurrent Unit (GRU):

The GRU model operates on fixed-length monthly sequences per customer. Each sequence captures the evolution of customer behavior over time using:

 -Monthly spending

 -Monthly invoice count

 -Monthly quantity

Missing months are explicitly represented as zero values to preserve temporal structure. This model is able to capture seasonality, inactivity patterns, and long-term dependencies, which explains its superior performance.

## Preprocessing pipeline

Key preprocessing steps include:

 -Data cleaning and removal of invalid transactions:

 -Outlier filtering at the customer level

 -Monthly aggregation per customer

 -Construction of fixed-length time windows

 -Logarithmic transformation to reduce skewness

 -Robust scaling fitted only on training data to prevent data leakage

Separate preprocessing pipelines are used for the FFNN and the GRU, depending on whether temporal structure is required.

## API application:

A FastAPI application is included to demonstrate practical usage of the trained GRU model.

Available endpoints:

POST /predict
Upload a CSV file with transactional data and receive CLV predictions per customer.

GET /topCustomers
Returns the top N customers ranked by predicted future value.

GET /metrics
Returns summary statistics of the latest prediction batch.

GET /segment
Automatically segments customers into low, medium, and high value groups based on quantiles.

GET /export
Exports the latest predictions as a downloadable CSV file.

Predictions are associated with customer IDs, allowing direct integration with downstream systems.
