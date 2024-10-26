#  **Wafer Fault Detection Project**

The objective of this project is to build a classification methodology to predict the quality of wafer sensors based on the given training data. The goal is to automate fault detection in semiconductor wafers, thereby improving quality control and manufacturing efficiency.

## Table of Contents

* [Problem Statement](#problem-statement)
* [Architecture](#architecture)
* [Data Description](#data-description)
* [Data Validation](#data-validation)
* [Data Insertion in Database](#data-insertion-in-database)
* [Model Training](#model-training)
* [Prediction Data Description](#prediction-data-description)
* [Prediction](#prediction)
* [Technologies Used](#technologies-used)
* [Directory Structure](#directory-structure)
* [Installation and Setup](#installation-and-setup)
* [Contact](#contact)

## Architecture

The project implements an end-to-end machine learning pipeline using a modularized architecture. It includes data ingestion, data validation, database storage, model training, and a prediction pipeline to classify wafer sensors as faulty or non-faulty.

## Data Description

The client sends multiple sets of data files in batches, each containing wafer names and 590 columns of sensor values for each wafer. The last column, labeled "Good/Bad," has two unique values:

* `+1` indicates a  **bad wafer** .
* `-1` indicates a  **good wafer** .

Along with the training files, a "schema" file is also provided by the client. This file contains details about the dataset, including:

* File names and patterns,
* Length of date and time in the file names,
* Number of columns, and
* Column names and their datatypes.

## Data Validation

The following validation checks are performed on the given training files:

1. **Name Validation:** A regex pattern is used to validate the file name based on the schema. It also checks the length of date and time values in the file names. Valid files are moved to the "Good_Data_Folder," and invalid files to the "Bad_Data_Folder."
2. **Number of Columns:** The number of columns in each file is validated against the schema. Files with mismatched column counts are moved to the "Bad_Data_Folder."
3. **Name of Columns:** The column names are validated against the schema. Files with invalid column names are moved to the "Bad_Data_Folder."
4. **Datatype of Columns:** The datatype of each column is validated against the schema when inserting the files into the database. Files with invalid datatypes are moved to the "Bad_Data_Folder."
5. **Null Values in Columns:** If any column in a file has all values as NULL, the file is moved to the "Bad_Data_Folder."

## Data Insertion in Database

1. **Database Creation and Connection:** A database is created with the given name. If the database already exists, a connection is established.
2. **Table Creation:** A table named "Good_Data" is created in the database for storing validated files. If the table already exists, new files are inserted into the existing table.
3. **Insertion of Files:** All files in the "Good_Data_Folder" are inserted into the table. Files with invalid datatypes are moved to the "Bad_Data_Folder."

## Model Training

1. **Data Export from Database:** The validated data is exported from the database as a CSV file for model training.
2. **Data Preprocessing:**
   * Null values are imputed using the KNN imputer.
   * Columns with zero standard deviation are removed.
3. **Clustering:** KMeans algorithm is used for clustering the preprocessed data. The number of clusters is selected dynamically using the "KneeLocator" function. A trained KMeans model is saved for future predictions.
4. **Model Selection:** Two algorithms, Random Forest and XGBoost, are used for training. The best model for each cluster is selected based on the AUC score. Both models are trained using GridSearch for hyperparameter tuning. The best-performing model for each cluster is saved.

## Prediction Data Description

The client sends multiple prediction files in batches. These files also contain wafer names and 590 sensor columns. A "schema" file is required to validate the incoming prediction files.

## Prediction

1. **Data Validation:** The same validation steps as the training data are performed on the prediction files.
2. **Data Export from Database:** The validated data is exported from the database as a CSV file for prediction.
3. **Data Preprocessing:**
   * Null values are imputed using the KNN imputer.
   * Columns with zero standard deviation are removed.
4. **Clustering:** The KMeans model created during training is loaded, and the clusters for the preprocessed prediction data are identified.
5. **Prediction:** The model for each cluster is loaded, and predictions are made based on the respective models. The predictions, along with the wafer names, are saved in a CSV file.

## Technologies Used

* **Python**
* **Flask**
* **Docker**
* **TensorFlow**
* **Machine Learning Libraries:** Scikit-learn, Pandas, NumPy, Imbalanced-learn
* **Database:** Sqlite
* **Cloud Integration:** AWS S3 for cloud storage of models and data
* **Logging:** Python’s logging library
* **Version Control:** Git
