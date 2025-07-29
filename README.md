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

---

### 📦 Dataset Overview: Autoferry Multi-Modal Maritime Sensor Dataset

The Autoferry, Multi-Modal Maritime Sensor Dataset is a publicly available dataset designed for research in autonomous navigation in maritime environments. It was collected as part of the autoferry project, which aims to develop safe and reliable autonomous ferry systems for short-distance sea transport.

This dataset includes:

- **LiDAR scans**: 3D point clouds capturing spatial information.
- **X-band radar images**: High-resolution radar views for low-visibility perception.
- **W-band radar images**: Complementary radar modality for different penetration depths.
- **GNSS**: GPS-based location metadata per frame.
- **Calibration files**: Transformation matrices to bring all data into a common base frame.

Scenarios include **Near Port**, **Outer Port**, **Single Island**, and **Twins Island**. This study focuses on the **Twins Island** scenario for synchronization and fusion analysis.

---

## 🔍 What’s Inside?!

### 1. Exploratory Data Analysis (EDA)

#### 🟧 LiDAR EDA
- Point cloud shape inspection and transformation
- Statistical summaries: mean, std, min, max per axis
- 12D feature vectors used in synchronization

#### 🟨 Radar EDA
- Image structure validation: shape and channel type
- Intensity distribution: histogram and percentiles
- 12D image-based feature vectors extracted

---

## 🔗 Sensor Synchronization Methods

Three distinct synchronization strategies are implemented and compared:

---

### 1️⃣ Deep Learning–Based Feature Synchronization
- **Radar features** extracted using pretrained **MobileNetV2**
- **LiDAR features**: statistical 12D vector
- **Similarity**: cosine similarity between CNN features and LiDAR stats
- **Advantage**: semantic-level matching

### 2️⃣ Statistical Feature-Based Synchronization
- Extracts handcrafted interpretable statistics
- Matches radar and LiDAR using cosine similarity on feature vectors
- Lightweight and interpretable

### 3️⃣ Timestamp-Based Synchronization
- Matches based on temporal proximity (within window, e.g., ±10 ms)
- Ideal for hardware-synchronized setups

Each method is implemented in a dedicated Python script. Results are saved in a unified format for inspection and downstream processing.

---

## 📤 Output

- **Synchronized LiDAR**: `.npy` (3D point cloud)
- **Synchronized Radar**: `.png` (radar snapshot)
- **GNSS metadata**: `.json` with timestamp, lat, lon
- **Summary CSV**: `synced_summary.csv` with shapes, timestamps, scores

---

## 📚 Scientific Inspiration

This work is inspired by several recent studies on multimodal synchronization and deep feature alignment:

1. **MutualForce: Learning Pose Correction and Feature Alignment for Vehicle Sensor Synchronization**  
   *Zhou et al., ICCV 2023*  
   [https://arxiv.org/abs/2303.09962](https://arxiv.org/abs/2303.09962)

2. **LiRaFusion: Long-range Radar and Camera Fusion for Robust Perception in All Weather Conditions**  
   *Wang et al., CVPR 2023*  
   [https://arxiv.org/abs/2303.11909](https://arxiv.org/abs/2303.11909)

3. **Radar and LiDAR Synchronization Using Deep Visual Feature Similarity**  
   *Inspired by general deep feature alignment techniques in multimodal learning*

This repository leverages and adapts the ideas of **deep feature similarity** and **interpretable statistical metrics** for practical sensor synchronization in maritime environments.

---

## 🚀 Getting Started

The codes run in Google Colab or a local Python environment with:

- `PyTorch`
- `PIL`
- `NumPy`
- `Matplotlib`
- `Scikit-learn`
- `Pandas`

Clone the repo and run any of the three synchronization methods to start exploring.

---
