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
│ └── s.xlsx
└── results/ # Output plots, metrics, and summaries

---

## Minimal Manuscript Examples

Below are minimal examples to run each script. Adjust file paths inside the scripts if needed.

### Feature Extraction

```bash python feature_extraction.py
Input raw Excel files (36 columns each) should be placed in data/excel/ (or modify the data_folder variable).

Extracted features will be saved in data/sensor_features/.

### Visual Area Measurement

python Visual_identification_of_the_area_occupied_by_the_stone_block.py
Follow the interactive prompts: first select the reference region (5 mm × 5 mm squares), then select the stone region.

The result (area in mm²) will be displayed on screen.

### Random Forest – Area Classification

python Random_Forest_for_Area_Classification_and_Recognition.py
Raw Excel files (36 columns) must be organised in class subfolders under data/excel/ (or modify base_path).

The script runs Optuna hyperparameter optimisation and displays t‑SNE, feature importance, and confusion matrix.

### CNN – Coordinate Regression

python Location_Recognition.py
Requires feature files from feature_extraction.py to be present in data/sensor_features/.

Also requires data/coordinate_set.xlsx with ground‑truth coordinates.

Outputs training curves, scatter plots, and importance heatmaps.

### Sensor Combination Evaluation (Random Forest)

python Different_sensor_combinations_identification.py
For Random Forest workflow: uses raw data from class subfolders; no extra input file needed.

Generates 100 four‑sensor combinations, evaluates them, and saves results in results/.

### Sensor Combination Evaluation (CNN)

python Different_sensor_combinations_identification.py
For CNN workflow: requires data/s.xlsx containing the list of predefined four‑sensor combinations (each row as a string, e.g., "[np.int64(2), np.int64(9), np.int64(18), np.int64(33)]").

Also requires feature files and coordinate file as above.

Outputs per‑combination metrics and a summary Excel file.

## Model Description
### Random Forest Classifier (for area classification)
Input: Flattened raw time‑series (36 sensors × time points) as a 1D vector.

Hyperparameter optimisation: Optuna with 50 trials, optimising:

n_estimators: number of trees (50–300)

max_depth: tree depth (5–50)

min_samples_split and min_samples_leaf

Evaluation: Accuracy, confusion matrix (percentage), feature importance (averaged per sensor).

Visualisation: t‑SNE projection of the feature space, top‑50 feature importance bar chart.

### CNN Regressor (for coordinate prediction)
Input: 504 features (36 sensors × 14 statistical/spectral features).

Architecture:

Two convolutional layers (1D) with batch normalisation and dropout.

Global average pooling.

Two fully connected layers (64 → 2) with ReLU activations.

Loss: Mean squared error (MSE) for (x, y) coordinates.

Data augmentation: Additive noise, scaling, and time‑shifting applied to the feature matrix during training.

Feature importance: Calculated by permuting each feature and measuring the increase in MSE; top‑50 features are used for a second training round.

Outputs: Training/validation loss curves, scatter plot of predicted vs. true coordinates with 95% confidence intervals, importance heatmap.

### Sensor Combination Evaluation
For Random Forest: The script evaluates 100 four‑sensor combinations (top‑4 most important sensors plus 99 variants where one sensor is randomly replaced). For each combination, a Random Forest is trained and tested.

For CNN: A user‑provided list of four‑sensor combinations is evaluated. Each combination uses the selected sensors’ features (4 sensors × 14 features = 56 features) to train a CNN. Additionally, a second CNN is trained on the top‑50 most important features (determined from the full‑sensor importance analysis).
