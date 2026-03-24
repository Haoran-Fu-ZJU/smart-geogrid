## Overview

This repository provides a set of tools for processing 36‑channel sensor data, extracting features, and performing both classification (Random Forest) and regression (CNN) tasks. It also includes an interactive visual tool for measuring the area occupied by a stone block in an image.

The workflow is organised as follows:

1. **Feature Extraction** – Extract statistical/spectral features from raw sensor Excel files.  
2. **Visual Area Measurement** – Interactively measure the area of a stone block using a reference calibration grid.  
3. **Sensor Data Analysis**  
   - **Random Forest Classification** – Classify impact areas using raw or extracted features.  
   - **CNN Regression** – Predict (x, y) coordinates from sensor features.  
   - **Sensor Combination Evaluation** – Assess the performance of different 4‑sensor layouts.

All dependencies are listed in `requirements.txt`.

---

## Directory Structure

```
├── README.md
├── requirements.txt
├── feature_extraction.py
├── Visual_identification_of_the_area_occupied_by_the_stone_block.py
├── Random_Forest_for_Area_Classification_and_Recognition.py
├── Location_Recognition.py
├── Different_sensor_combinations_identification.py # shared between both workflows
├── data/
│ ├── excel/ # Raw sensor Excel files
│ ├── sensor_features/ # Extracted feature files
│ ├── coordinate_set.xlsx
│ └── sensor_combination.xlsx
└── results/ # Output plots, metrics, and summaries
```

---

## Minimal Manuscript Examples

Below are minimal examples to run each script. Adjust file paths inside the scripts if needed.
Some raw data used in the manuscript are not included in this repository because of file size limits and were uploaded separately during manuscript submission.  
Please prepare the required input files and update file paths in the scripts if needed.

### Feature Extraction

```bash
python feature_extraction.py
```
Input raw Excel files (36 columns each) should be placed in data/excel/ (or modify the data_folder variable).

Extracted features will be saved in data/sensor_features/.

### Visual Area Measurement
```bash
python Visual_identification_of_the_area_occupied_by_the_stone_block.py
```

Follow the interactive prompts: first select the reference region (5 mm × 5 mm squares), then select the stone region.

The result (area in mm²) will be displayed on screen.

### Random Forest – Area Classification

```bash
python Random_Forest_for_Area_Classification_and_Recognition.py
```

Raw Excel files (36 columns) must be organised in class subfolders under data/excel/ (or modify base_path).

The script runs Optuna hyperparameter optimisation and displays t‑SNE, feature importance, and confusion matrix.

### CNN – Coordinate Regression

```bash
python Location_Recognition.py
```

Requires feature files from feature_extraction.py to be present in data/sensor_features/.

Also requires data/coordinate_set.xlsx with ground‑truth coordinates.

Outputs training curves, scatter plots, and importance heatmaps.

### Sensor Combination Evaluation (Random Forest)

```bash
Different_sensor_combinations_identification.py
```

For Random Forest workflow: uses raw data from class subfolders; no extra input file needed.

Generates 100 four‑sensor combinations, evaluates them, and saves results in results/.

### Sensor Combination Evaluation (CNN)

```bash
Different_sensor_combinations_identification.py
```

For CNN workflow: requires data/sensor_combination.xlsx containing the list of predefined four‑sensor combinations (each row as a string, e.g., "[np.int64(2), np.int64(9), np.int64(18), np.int64(33)]").

Also requires feature files and coordinate file as above.

Outputs per‑combination metrics and a summary Excel file.

---

## Model Description
### Random Forest Classifier (for area classification)

Data loading:
Loads Excel files from subfolders under sensor_features (each subfolder name corresponds to a class label).

Hyperparameter optimisation:

Bayesian optimisation with Optuna (50 trials) to maximise the 5‑fold cross‑validation accuracy of a random forest classifier。

Model training:
A final random forest classifier is trained on the full training set using the best hyperparameters found by Optuna.

Evaluation:
Accuracy on the held‑out test set (20% stratified split).

Classification report (precision, recall, f1‑score per class).

Confusion matrix plotted as percentages to visualise per‑class misclassification rates.

Visualisation:

t‑SNE projection: Dimensionality reduction of the training feature space to 2D, coloured by class, to assess separability.

Feature importance: Bar chart showing the top‑50 most important features (each feature corresponds to a specific sensor and statistical/spectral descriptor, e.g., “Sensor 1 mean”). Importance is derived from the random forest’s built‑in feature_importances_ attribute.
Visualisation: t‑SNE projection of the feature space, top‑50 feature importance bar chart.

### CNN Regressor (for coordinate prediction)
Input: 504 features (36 sensors × 14 statistical/spectral features).

Architecture: Three convolutional layers (1D) with ReLU and max pooling, followed by flattening and two fully connected layers (512*1 → 256 → 2).
Loss: Custom loss combining MSE, correlation loss, and individual coordinate MSE.

Data augmentation: Additive noise, scaling, and shifting applied to the feature matrix during training.

Feature importance: Calculated from convolutional layer weights; top‑50 features are selected for a second training round using a simplified CNN.

Outputs: Training/validation loss curves, scatter plot of predicted vs. true coordinates with 95% confidence intervals, feature importance heatmap.

### Sensor Combination Evaluation
For Random Forest: The script evaluates 100 four‑sensor combinations (top‑4 most important sensors plus 99 variants where one sensor is randomly replaced). For each combination, a Random Forest is trained and tested.

For CNN: A user‑provided list of four‑sensor combinations is evaluated. Each combination uses the selected sensors’ features (4 sensors × 14 features = 56 features) to train a CNN.
