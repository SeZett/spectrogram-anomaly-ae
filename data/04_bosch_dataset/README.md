## Bosch CNC Dataset

This project uses vibration data from the Bosch CNC Machining dataset: https://github.com/boschresearch/CNC_Machining/tree/main/data

Raw `.h5` files are converted into fixed-size spectrogram images to enable consistent input for machine learning models.


## Segmentation Strategy

To ensure comparability across signals of varying length, time series are segmented into fixed-length windows.

The window size defines a trade-off between temporal resolution and robustness:
- Smaller windows enable detection of short-lived, localized anomalies
- Larger windows provide more stable and less noisy spectral representations

In this work, a window size of **5 seconds** is used as a compromise between capturing localized defects and maintaining sufficient context for reliable spectrogram generation.

## Preprocessing Steps

1. Load multi-axis vibration signals (X, Y, Z) from `.h5` files  
2. Segment each time series into fixed-length windows (e.g. 5 seconds)  
3. Generate spectrograms for each segment using STFT  
4. Combine three axes into RGB images  
5. Resize images to a fixed resolution (e.g. 150 × 100)  


## Output

Each input file is converted into multiple spectrogram images.
