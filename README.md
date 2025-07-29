# Multi-Sensor-Maritime-Dataset-Processing-and-Synchronization


<div align="center">
  <table border=0 style="border: 0px solid #c6c6c6 !important; border-spacing: 0px; width: auto !important;">
    <tr>
      <td valign=top style="border: 0px solid #c6c6c6 !important; padding: 0px !important;">
        <div align=center valign=top>
          <img src="https://github.com/NaderNemati/Multi-Sensor-Maritime-Dataset-Processing-and-Synchronization/blob/main/image/Multimodal%20Data%20Fusion%20Visualization.png" style="margin: 0px !important; height: 400px !important;">
        </div>
      </td>
    </tr>
  </table>
</div>

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

## Output

After running any of the three synchronization pipelines, a set of structured and analysis-ready output files is generated in the synced_output/ directory. This allows for easy inspection, downstream fusion tasks, or training data preparation for machine learning models.

##### 1. Synchronized LiDAR Frames (.npy)

    Each synchronized LiDAR point cloud is saved as a NumPy binary file using the format:

The file contains a 2D array of shape (N, 3) where each row represents a 3D point with (x, y, z) coordinates.

The point cloud is already transformed using the Base2LiDAR calibration matrix to place all data in the same spatial reference frame.

##### 2. Synchronized Radar Images (.png)

    The radar (X-band) images that best match the LiDAR frames (either by timestamp or feature similarity) are saved as .png files.

For deep learning synchronization, radar images are converted to RGB before processing, but grayscale is retained in the output to save space.

These images are useful for visual inspection, EDA, or use as input to CNN models.

##### 3. GNSS Metadata (.json)

    For each synchronized pair, the closest GNSS reading (based on timestamp) is saved as a .json file.

This JSON file contains location metadata such as:
    {
  "timestamp": 1724625908715105792,
  "latitude": 1.246756,
  "longitude": 103.841014,
  "rosbag_file": "...",
  ...
    }

Useful for mapping, trajectory tracking, or geospatial visualization of the synchronized data.

##### 4. Synchronization Summary File (synced_summary.csv)

    A CSV summary file that consolidates metadata and quality metrics for all matched frames.

Columns include:

    index: Match index

    timestamp: LiDAR frame timestamp

    lidar_shape: Shape of saved LiDAR array

    xband_shape: Size of the radar image

    similarity_score: Cosine similarity between extracted features (deep/statistical)

    gnss_lat, gnss_lon: Coordinates from GNSS metadata

Sample:
...
        index,timestamp,lidar_shape,xband_shape,similarity_score,gnss_lat,gnss_lon
        0,1724625908715105792,"(32000, 3)","(2048, 2048)",0.0927,1.246756,103.841014
...


Enables quick filtering, performance evaluation, or visualization of synchronization quality over time.


Together, these outputs provide a clean and rich dataset for multimodal sensor fusion tasks that enable downstream modeling like object detection, scene understanding, or spatial-temporal trajectory analysis.

### Getting Started

The codes are designed to run in environments like Google Colab or a local Python setup with:

    PyTorch

    PIL

    NumPy

    Matplotlib

    Scikit-learn

    Pandas


