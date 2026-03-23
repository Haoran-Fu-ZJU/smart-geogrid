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

## Minimal Manuscript Examples

### 1. Feature Extraction
```bash
python feature_extraction.py
This reads raw Excel files from the excel folder and saves extracted features to sensor_features.
2. Visual Area Measurement
bash
python Visual_identification_of_the_area_occupied_by_the_stone_block.py
Follow the on‑screen instructions to select the reference squares and the stone region. The computed area will be displayed.

3. Random Forest – Area Classification
bash
python Random_Forest_for_Area_Classification_and_Recognition.py
This script trains a Random Forest classifier on raw time‑series data and generates visualisations.

4. CNN – Coordinate Regression
bash
python Location_Recognition.py
This script loads pre‑computed features and trains a CNN to predict (x, y) coordinates.

5. Sensor Combination Evaluation
For Random Forest (automatic combination generation):

bash
python Different_sensor_combinations_identification.py
For CNN (predefined combinations):
Ensure s.xlsx is present, then run:

bash
python Different_sensor_combinations_identification.py
Model Description
Random Forest – Area Classification
Input: Raw time‑series (flattened) from 36 sensors.

Model: Random Forest classifier with hyperparameter optimisation using Optuna.

Output: t‑SNE visualisation, feature importance bar chart, confusion matrix.

Sensor combination evaluation: Per‑sensor importance is computed (averaged over the 14 features). 100 combinations of 4 sensors are generated (top‑4 plus random replacements). Each combination is trained and evaluated with the same Random Forest pipeline. Results include confusion matrices (PNG), accuracy distribution plot, and a summary CSV (top4_sensor_combination_results.csv).

CNN – Coordinate Regression
Input: Feature files from feature_extraction.py (14 features per sensor → 504 features total).

Model: Convolutional Neural Network (CNN) with data augmentation (noise, scaling, shifting). The model predicts continuous (x, y) coordinates.

Output: Training curves, scatter plots with confidence intervals, feature importance heatmaps, metrics JSON.

Sensor combination evaluation: A predefined list of 4‑sensor combinations is read from s.xlsx. For each combination, the CNN is trained on the selected sensors’ features (56 features) and separately on the top‑50 most important features (from full‑set analysis). Results include per‑combination R² scores, metrics, and a summary Excel file.

Both workflows use 36 sensors and 14 features per sensor by default; constants can be adjusted in the scripts if needed.

