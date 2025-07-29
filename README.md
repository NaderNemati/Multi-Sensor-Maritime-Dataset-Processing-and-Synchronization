# Multi-Sensor-Maritime-Dataset-Processing-and-Synchronization

### Dataset Overview: Autoferry Multi-Modal Maritime Sensor Dataset

The Autoferry, Multi-Modal Maritime Sensor Dataset is a publicly available dataset designed for research in autonomous navigation in maritime environments. It was collected as part of the autoferry project, which aims to develop safe and reliable autonomous ferry systems for short-distance sea transport.

This dataset includes the following components:

LiDAR scans: 3D point clouds capturing spatial information about the environment.

X-band radar images: High-resolution radar captures to detect distant or low-visibility obstacles.

W-band radar images: Complementary radar data for object detection under various conditions.

GNSS (Global Navigation Satellite System): Precise latitude and longitude for each frame.

Calibration files: Transformation matrices to align all sensor data into a common reference frame.

Moreover, this dataset includes different scenarios, Near Port, Outer Port, Single Island, and Twins Island, that provide a challenging environment for sensor fusion, synchronization, and autonomous decision-making. Our study specifically focuses on synchronization for the Twins Island scenario! This repository provides a comprehensive pipeline for processing, analyzing, and synchronizing LiDAR, Radar (X-band, W-band), and GNSS sensor data collected in maritime environment.


## What’s Inside?!

#### 1- Exploratory Data Analysis (EDA)

### LiDAR EDA

The LiDAR point cloud data contains spatial information in the form of 3D coordinates (X, Y, Z), sometimes with intensity or reflectivity channels. The following analyses were performed:

Point Cloud Structure Inspection:

1-Validation of dimensionality and shape ((N, 4) → keeping only (X, Y, Z))

2-Transformation to a common base frame using calibration matrices.


Statistical Summaries:

Calculation of mean, standard deviation, min/max values along each axis (X, Y, Z).

Additional statistics, like bounding box extents to assess the physical area covered.

Feature Vectors:

Computation of 12D feature vectors from each LiDAR frame:
    
    [mean_x, mean_y, mean_z, std_x, std_y, std_z, max_x, max_y, max_z, min_x, min_y, min_z]

These are used for feature-based synchronization.


### Radar EDA

The radar data includes X-band (and optionally W-band) radar images saved as .png files. These are typically grayscale but may also include RGB-converted versions. Radar EDA focuses on:

Image Structure Analysis:

Validation of image dimensions (e.g., 2048×2048) and color channels (grayscale vs. RGB).

Conversion to grayscale when required for consistency.

Intensity Distribution:

Histograms of pixel intensities to understand contrast, noise, and signal strength.

Percentile-based features such as P10, P25, P50, P75, P90, P95 for feature extraction.

Feature Vectors:

    12D feature vectors computed per radar image:
    [mean, std, max, min, P10, P25, P50, P75, P90, P95, std/mean, max - min]


## Sensor Synchronization Methods

To align data captured from multiple heterogeneous sensors (LiDAR and radar), three distinct synchronization strategies have been implemented and systematically compared. Each approach is tailored to different system setups, offering trade-offs between accuracy, interpretability, and computational efficiency.

#### 1️⃣ Deep Learning–Based Feature Synchronization

Purpose: Align radar images with LiDAR point clouds using high-level semantic features extracted from a convolutional neural network.

     Architecture:

        Utilizes pretrained MobileNetV2, a lightweight CNN, to extract 1280-dimensional feature vectors from radar (X-band) images.

        Converts each image to RGB, resizes it to 128×128, and applies ImageNet normalization.

     LiDAR Feature Representation:

        Instead of raw point clouds, descriptive statistics (mean, std, min, max) across XYZ axes are extracted to create a compact feature vector.

     Similarity Metric:

        Cosine similarity is computed between the radar CNN features and LiDAR statistical features (truncated to match dimensionality).

     Advantages:

        Captures high-level visual semantics (e.g., shapes, textures) from radar images.

        Can tolerate moderate sensor noise or temporal misalignment.

     Limitations:

        Requires image conversion to RGB even if radar is inherently grayscale.

        Slight mismatch in feature modality may affect precision.

#### 2️⃣ Statistical Feature-Based Synchronization

Purpose: Synchronize frames based on directly interpretable statistical characteristics of sensor data.

     Radar Features:

        From each grayscale radar image, 12 statistical features are extracted:
        [mean, std, max, min, percentiles (P10–P95), std/mean, dynamic range]

     LiDAR Features:

        Same 12D vector as above: mean, std, max, and min values across X, Y, Z axes.

     Similarity Metric:

        Cosine similarity is calculated between the 12-dimensional LiDAR and radar feature vectors.

     Advantages:

        Fully interpretable and does not require deep learning.

        Lightweight and fast — suitable for embedded systems.

     Limitations:

        Might not capture high-level semantic cues (e.g., object shape) in radar images.

        Sensitive to sensor noise and outliers in the raw data.

#### 3️⃣ Timestamp-Based Synchronization

Purpose: Align frames purely based on their recorded timestamps, assuming accurate and synchronized clocks.

     Mechanism:

        For each LiDAR frame timestamp, searches for the closest radar frame timestamp within a configurable temporal tolerance window (e.g., 10 ms).

     Advantages:

        Fastest and simplest method — no feature computation required.

        Ideal for well-calibrated acquisition systems where timestamps are accurate and aligned.

     Limitations:

        Fails under clock drift or imprecise logging, which is common in distributed sensor platforms.

        Does not handle content mismatch or dropped frames.



Each method is implemented as a script, and the results are stored in a unified format for comparing downstream fusion tasks. Visualization and statistics are provided to inspect the synchronization quality.

