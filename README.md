# SME ML Demand Forecasting to Operational Decisions Standardized Pipeline

This repository contains a standardized forecasting-to-operational-decision pipeline designed to support the pre-adoption evaluation of machine-learning demand forecasting for small and medium-sized enterprises (SMEs).

Rather than comparing forecasting methods only on statistical accuracy, the pipeline evaluates whether more complex machine-learning approaches generate meaningful improvements in simulated inventory outcomes under the operating conditions of a specific firm.

The pipeline is designed for businesses using Shopify and works directly from a standard Shopify order-export CSV.

## What the pipeline does

The pipeline processes transaction-level order data and implements:

- weekly SKU-level demand preparation;
- ABC-XYZ portfolio analysis and demand segmentation;
- lower-complexity forecasting methods:
  - Naive;
  - Seasonal Naive;
  - Moving Average;
  - Exponential Smoothing (ETS);
  - Teunter-Syntetos-Babai (TSB);
- machine-learning forecasting methods:
  - Random Forest;
  - Gradient Boosting;
- rolling one-step forecast evaluation;
- protection-period forecast evaluation;
- method-specific safety-stock calibration;
- periodic-review, order-up-to inventory simulation;
- comparison of forecast accuracy and operational value;
- sensitivity analysis for:
  - lead time;
  - stockout cost;
  - minimum order quantities and batching constraints;
  - target service level.

The objective is not to identify a universally superior forecasting method. Instead, the pipeline evaluates whether the additional complexity of machine learning produces sufficient operational value under the demand and replenishment conditions of the individual business.

## Input data

The pipeline expects a standard Shopify order-export CSV containing line-level order data.

To export the required data from Shopify, follow the official Shopify instructions:

[Shopify – Exporting orders and order CSV structure](https://help.shopify.com/it/manual/fulfillment/managing-orders/exporting-orders#order-export-csv-structure)

Select a historical period of **at least 18 months** and export the orders as a CSV file.

A longer history is recommended where available, particularly when seasonal demand patterns need to be evaluated.

The pipeline converts the transaction-level export into weekly demand series at SKU level.

## Configuration

Before running the pipeline, update the configuration section of the notebook with the parameters that describe the business being analyzed.

The main inputs include:

- path to the Shopify CSV file;
- supplier lead time;
- inventory review period;
- target service level;
- relative stockout cost;
- minimum order quantity or batching constraints;
- length of the final out-of-sample evaluation period.

These parameters allow the forecasting methods to be evaluated under operating conditions that approximate the firm's actual replenishment environment.

## Evaluation logic

The pipeline evaluates forecasting methods at two levels.

### Forecast performance

Forecasts are evaluated out of sample using rolling-origin evaluation. Performance is assessed both:

- one week ahead; and
- across the full protection period, defined as lead time plus review period.

The primary accuracy measure is WAPE, with MASE used as a complementary metric.

### Operational performance

Each forecasting method is translated into replenishment decisions using the same periodic-review, order-up-to inventory policy.

Safety stock is calibrated separately for each forecasting method using historical protection-period forecast errors.

The resulting policies are compared using:

- fill rate;
- units short;
- average inventory;
- inventory expressed in weeks of demand;
- normalized inventory-related cost.

This makes it possible to identify cases in which better forecast accuracy does not necessarily result in better inventory performance.

## Scenario and sensitivity analysis

The pipeline tests whether the comparison between lower-complexity and ML forecasting remains stable when operating assumptions change.

Sensitivity analysis can vary:

- supplier lead time;
- stockout-cost severity;
- MOQ and order-batching constraints;
- target service levels.

The resulting scenario analysis helps distinguish a robust operational advantage from one that depends on a narrow set of assumptions.

## Requirements

The pipeline is implemented in Python and provided as a Jupyter Notebook.

Main dependencies include:

- pandas
- numpy
- scikit-learn
- statsmodels
- matplotlib

Additional dependencies used by the notebook can be installed as required.

## How to use

1. Export at least 18 months of order history from Shopify.
2. Save the CSV file anywhere on your computer.
3. Open `Standardized_SME_Forecasting_Pipeline.ipynb`.
4. In the configuration section:
   - set `csv_path` to the location of the Shopify CSV;
   - set the lead time;
   - set the review period;
   - set the service target;
   - set the stockout-cost assumption;
   - set any MOQ or batching constraints.
If `csv_path` is left as `None`, the notebook falls back to the default `data/raw/` project-folder structure.
5. Run the notebook sequentially.
6. Review the forecast-performance, inventory-performance, and sensitivity-analysis outputs.
7. Compare the best-performing lower-complexity and machine-learning approaches before considering the additional complexity of ML.

## Important considerations

The pipeline is intended as a pre-adoption evaluation tool rather than a production inventory-management system.

Results are conditional on the quality and coverage of the available transaction data and on the operating assumptions provided by the user. Shopify recorded sales may also understate underlying demand during periods in which products were unavailable.

The simulated cost measures are intended for relative comparison between forecasting methods and should not be interpreted as a complete financial ROI or total-cost-of-ownership calculation.
