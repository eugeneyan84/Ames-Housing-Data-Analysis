# Ames Housing Data Analysis

## Problem Statement
This project aims to build a predictive model to forecast the sale price of properties in Ames, Iowa. A linear regression model would be constructed using feature data from the Ames Housing Dataset, which documents over 70 attributes of property sold in the abovementioned city between 2006 to 2010. 

Scope of this project would encompass data cleaning, exploratory analysis of the distributions of the feature values, feature engineering of new attributes that may aid better prediction, followed by model selection, tuning and evaluation.  A set of performance metrics would be defined to steer the evaluation of the models built.

The goal of the project is to show that the characteristics of a property (internal ones such as type and quality of construction, and external ones like locality) do exhibit intrinsic influence on its sale price, thus the accurate prediction of a property's market price could facilitate due diligence checks, such as appraisals for mortgage financing.

## Executive Summary

This project is focused on the development of a housing price prediction model from the use of feature and price data on properties sold in Ames, Iowa between 2006 to 2010. It is subsequently demonstrated here that sale prices are indeed influenced by internal traits of the property being sold, as well as external factors affecting it. Project phases have been broken down into 3 notebooks.

In *Data Cleaning & Exploratory Data Analysis* phase, the Ames housing dataset underwent a series of data cleaning procedures to correct minor errors, as well as impute missing data from a number of columns. The dataset was analysed for distribution of values among the many nominal and ordinal columns for the purpose of identifying severely skewed columns that were subsequently dropped. Criteria for dropping columns include sparse population (<10% of all observations), as well as overwhelming presence of modal value (<10% observation coverage for non-modal values). Exploratory data analysis revealed positive relationship between many continuous variables and `SalePrice`, especially for area-centric features. Boxplot of nominal variables, such as `Neighborhood`, revealed distinct inter-quartile range variations in price that indicated the effects of locality on property price. Initial heatmap analysis revealed how a area-centric variables, quality scores and condition ratings correlating in a strong positive manner with `SalePrice`. A total of 2 rows (outliers declared in DataDocumentation.txt) and 14 columns were dropped from the train dataset at this juncture of the project.

In *Pre-processing & Feature Engineering* phase, one-hot-encoding of nominal features and a 2nd round of heatmap analysis revealed that they do not exhibit strong correlation with `SalePrice` compared to continuous and ordinal variables. Feature Engineering phase allowed creation of 4 new features, one of which was `Neighborhood Score`, which not only replaced the 28 one-hot-encoded columns with a single strongly-correlated feature, but also proved 1 of the goals that such an external factor can have significant positive correlation with property price.

In *Model Training, Tuning & Benchmarking* phase, performance metrics centered around test-set Root Mean-Squared Error (RMSE) and the gap between train-set and test-set RMSEs (termed as RMSE Degradation). Both metrics, as well as inspection of model coefficients, provided a means to compare and steer the development from the 1st model (`naive-means`) that relied on naïve means projection, to the 7th one (`ElasticNet-1`) that utilised ElasticNet model with optimised regularization and l1-ratio parameters. Linearing Regression with 101 features (`LR-1`) resulted in a model with coefficients having overblown scales, and was not neither explainable nor inferable. 

`ElasticNet-1` was the chosen production model, due to its slightly better performance over the intermediate models, as well as being able to generalize over a final set of 24 predictors. However, test-set RMSE and RMSE Degradation scores fell short of expectation after the final Kaggle submission. In conclusion, while `ElasticNet-1` scored a test-set RMSE of <b>30168.1</b> on Kaggle, which is 18.8% worse than *LR-1*, the former model could predict with 4 times lesser features than the latter, was more stable in terms of coefficient analysis, and ultimately able to meet the project goal of demonstrating the influence of internal and external factors on the sale price of a property.

## Model Performance Table
| Model Name       | Model Type       | Number of Features                         | R2 score | Train-set RMSE | Test-set RMSE    | RMSE Degradation (%) |
|------------------|------------------|--------------------------------------------|----------|----------------|------------------|----------------------|
| naive-means      | N.A.             | 0                                          | N.A.     | 78759.5        | 81310.7          | 3.24                 |
| LR-1             | LinearRegression | 101\*                                        | 0.902524 | 22076.1        | 25395.7          | 15.04                |
| LR-2             | LinearRegression | 40\*\*                                         | 0.887381 | 25367.6        | 28217.5          | 11.23                |
| Ridge-1          | Ridge            | 40\*\*                                         | 0.887317 | 25370.4        | 28185.5          | 11.1                 |
| Lasso-1          | Lasso            | 40\*\*                                         | 0.886918 | 25417.8        | 28077            | 10.46                |
| Lasso-2          | Lasso            | 37 (reduction by Lasso-1)                  | 0.886973 | 25417.8        | 28077            | 10.46                |
| ElasticNet-1     | ElasticNet       | 24 (reduction by OLS backward elimination) | 0.888179 | 25523.6        | 27976.2          | 9.61                 |
| Production Model | ElasticNet       | 24                                         | 0.892675 | 25971.4        | 30168.1 (Kaggle) | 16.2                 |

<sub>\* 101 features filtered from correlation score comparison with `SalePrice`, cutoff at score of 0.10 (see *Pre-processing & Feature Engineering* notebook)</sub>
  
<sub>\*\* 40 features filtered from correlation score comparison with `SalePrice`, after engineering new features, and cutoff score of 0.20 (see *Pre-processing & Feature Engineering* notebook)</sub>

## Data Dictionary
Based on `x_train_final.csv` feature dataset for selected production model.


<sub><u>Abbreviations</u></sub>
<sub>OHE: One-Hot Encoded</sub>
<sub>FE: Feature Engineered</sub>


| Column               | Type  | Origin                               | Description                                                                                     |
|----------------------|-------|--------------------------------------|-------------------------------------------------------------------------------------------------|
| Total SF             | float | FE                                   | Total area of property in square feet                                                           |
| Overall Qual         | int   | train.csv                            | Rates the overall material and finish of the house                                              |
| Interior Qual        | int   | FE                                   | Sum of interior-centric quality scores and condition ratings of the house                       |
| Neighborhood Score   | float | FE                                   | Mean of the sum of all quality scores and condition ratings of properties within a neighborhood |
| Exter Qual           | int   | train.csv                            | Evaluates the quality of the material on the exterior                                           |
| Year Remod/Add       | int   | train.csv                            | Remodel date (same as construction date if no remodeling or additions)                          |
| Foundation_PConc     | int   | OHE from `Foundation` in train.csv   | 1 indicates poured concrete foundation, 0 if otherwise                                          |
| BsmtFin SF 1         | float | train.csv                            | Rating of basement finished area (if multiple types)                                            |
| Bsmt Exposure        | float | train.csv                            | Refers to walkout or garden level walls                                                         |
| Mas Vnr Type_None    | int   | OHE from `Mas Vnr Type` in train.csv | 1 indicates no masonry veneer, 0 if otherwise                                                   |
| Garage Type_Detchd   | int   | OHE from `Garage Type` in train.csv  | 1 indicates detached garage, 0 if otherwise                                                     |
| Sale Type_New        | int   | OHE from `Sale Type` in train.csv    | 1 indicates new sale type, 0 if otherwise                                                       |
| MS SubClass_C060     | int   | OHE from `MS SubClass` in train.csv  | 1 indicates 2-story 1946 or new, 0 if otherwise                                                 |
| Foundation_CBlock    | int   | OHE from `Foundation` in train.csv   | 1 indicates cinder block foundation, 0 if otherwise                                             |
| Lot Frontage         | float | train.csv                            | Linear feet of street connected to property                                                     |
| Mas Vnr Type_Stone   | int   | OHE from `Mas Vnr Type` in train.csv | 1 indicates stone masonry veneer, 0 if otherwise                                                |
| Lot Area             | int   | train.csv                            | Lot size in square feet                                                                         |
| Half Bath            | int   | train.csv                            | Basement half bathrooms                                                                         |
| Roof Style_Hip       | int   | OHE from `Roof Style` in train.csv   | 1 indicates hip roof type, 0 if otherwise                                                       |
| Mas Vnr Type_BrkFace | int   | OHE from `Mas Vnr Type` in train.csv | 1 indicates brickface masonry veneer, 0 if otherwise                                            |
| Garage Type_NA       | int   | OHE from `Garage Type` in train.csv  | 1 indicates no garage, 0 if otherwise                                                           |
| Foundation_BrkTil    | int   | OHE from `Foundation` in train.csv   |                                                                                                 |
| Garage Type_BuiltIn  | int   | OHE from `Garage Type` in train.csv  | 1 indicates built-in garage, 0 if otherwise                                                     |
| Land Contour_HLS     | int   | OHE from `Land Contour` in train.csv | 1 indicates hillside with significant slope from side to side, 0 if otherwise                   |

For data dictionary on `train.csv`, please refer to *DataDocumention.txt* within this project.

## Required Libraries

- pandas 1.1.3
- numpy 1.19.2
- matplotlib 3.3.2
- seaborn 0.11.0
- statsmodels 0.12.0
- IPython 7.18.1
- sklearn 0.23.2

