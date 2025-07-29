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




