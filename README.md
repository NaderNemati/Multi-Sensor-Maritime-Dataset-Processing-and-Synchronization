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
LiDAR EDA

The LiDAR point cloud data contains spatial information in the form of 3D coordinates (X, Y, Z), sometimes with intensity or reflectivity channels. The following analyses were performed:

    Point Cloud Structure Inspection:

        Validation of dimensionality and shape ((N, 4) → keeping only (X, Y, Z))

        Transformation to a common base frame using calibration matrices.

    Statistical Summaries:

        Calculation of mean, standard deviation, min/max values along each axis (X, Y, Z).

        Additional statistics, like bounding box extents to assess the physical area covered.

    Feature Vectors:

        Computation of 12D feature vectors from each LiDAR frame:
        [mean_x, mean_y, mean_z, std_x, std_y, std_z, max_x, max_y, max_z, min_x, min_y, min_z]
        These are used for feature-based synchronization.

    Visualization:

        Point cloud visualizations and scatter plots for inspecting spatial distributions and density.

        Helpful to identify outliers, empty scans, or misalignment due to faulty calibration.



