# spectrogram-anomaly-ae
Code accompanying the paper on unsupervised anomaly detection in vibration signals using convolutional autoencoders and spectrogram representations.

## Dataset

### Industrial Broaching Dataset

The proposed anomaly detection methodology was initially developed using an industrial broaching dataset in collaboration with Forst Technologie.

Due to confidentiality restrictions, the original vibration time-series cannot be made publicly available. However, the spectrogram representations used in this study are provided via Git LFS under `data/03_broach_dataset`.

The dataset was used to develop and evaluate the convolutional autoencoder and the Segmented Vertical Error (SVE) anomaly score.

### Turning Dataset

The proposed methodology was subsequently applied to the public turning/chatter diagnosis dataset hosted on Mendeley Data:

> Khasawneh, F., Otto, A., & Yesilli, M. (2019). *Turning Dataset for Chatter Diagnosis Using Machine Learning* (Version 1) [Data set]. Mendeley Data. 
> https://doi.org/10.17632/hvm4wh3jzx.1

The dataset is distributed as `.mat` files containing the time vector (`t`) and multi-sensor vibration signals (`d`). In this repository, we extract the accelerometer channels, convert windowed time-series into spectrogram representations (RGB images), and use these inputs to run unsupervised anomaly detection experiments with convolutional autoencoders.

## Notebook Workflow

### Industrial Broaching Dataset

The experiment workflow is organized as a numbered notebook series:

| Notebook | Purpose |
|---|---|
| `notebooks/broach_dataset/02_Create_Frozen_Splits_and_Manifests.ipynb`| Create a deterministic train/validation/test manifest using a stratified validation/test split with a fixed random seed.|
| `notebooks/broach_dataset/04_Train_CNN_AE_BN16_150x100px.ipynb`| Train the convolutional autoencoder on nominal broaching spectrograms.|
| `notebooks/broach_dataset/05_SVE_Sensitivity_Analysis_NestedCV_ValidationOnly.ipynb`| Perform validation-only sensitivity analysis to select robust Segmented Vertical Error (SVE) parameters using repeated nested cross-validation.|
| `notebooks/broach_dataset/06_AE_Final_Evaluation_SVE.ipynb`| Evaluate the trained autoencoder on the independent test set using the validation-selected SVE configuration and a frozen decision threshold.|
| `notebooks/broach_dataset/07_AE_Robustness_Analysis_Repeated_Stratified_CV.ipynb`| Assess the robustness of reconstruction-based anomaly scores across different validation and test set compositions.|

> **Note:** Steps 01 (raw-data preprocessing) and 03 (spectrogram generation) are not included for the broaching dataset because the original vibration time-series cannot be made publicly available.


### Turning Dataset
The experiment workflow is organized as a numbered notebook series:

| Notebook | Purpose |
|---|---|
| `notebooks/01_Load_Data_Segmentation_Labeling.ipynb` | Download and extract the public turning dataset, segment the accelerometer signals into 2.5-second windows, and assign chatter/no-chatter labels based on spectral chatter signatures. |
| `notebooks/02_Create_Frozen_Splits_and_Manifests.ipynb` | Create deterministic train/validation/test manifests. |
| `notebooks/03_Create_Spectrogram_Datasets.ipynb` | Generate spectrogram image datasets from the frozen manifest. |
| `notebooks/04_Train_CNN_AE_BN16_150x100px.ipynb` | Train the main CNN autoencoder on nominal training samples. |
| `notebooks/05_Evaluate_AE_Scores_and_Thresholds.ipynb` | Score autoencoder reconstructions and freeze validation-selected decision thresholds. |
| `notebooks/06a_Baseline_Comparisons_FixedDataSplit.ipynb` | Compare classical anomaly detection baselines using the same fixed train/validation/test split as the autoencoder. |
| `notebooks/06b_Baseline_Comparisons_NestedCV.ipynb` | Compare classical anomaly detection baselines using nested cross-validation to assess performance across different data splits|
| `notebooks/07_Baseline_Robustness_Analysis_NestedCV.ipynb` | Assess the robustness and variability of the baseline methods across different data splits using nested cross-validation. |
| `notebooks/08_Bootstrap_CIs_and_Report_Tables.ipynb` | Compute bootstrap confidence intervals for evaluation metrics and generate summary tables for reporting. |
| `notebooks/09_Publication_Quality_Figures_and_Tables.ipynb` | Create paper-ready PDF/SVG/PNG figures and CSV/LaTeX tables. |

The first notebook downloads the original Mendeley dataset, extracts the raw `.mat` files, and creates labeled 2.5-second windowed vibration segments.

The notebook saves processed segments to `data/01_windowed_labeled_2,5s`.

#### Windowed Data Format (`.npz`)

Each file contains a single 2.5-second vibration segment:

| Field | Description |
|---|---|
| t | time vector |
| X, Y, Z | accelerometer signals (3-axis vibration) |
| label | binary class (chatter / no_chatter) |
| A_time | RMS vibration amplitude |
| is_chatter | boolean decision from spectral analysis |
