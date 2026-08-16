# 🛣️ Automated Road Damage Assessment & Scoring Engine

An automated road damage analysis and scoring system based on **Computer Vision** using the **YOLOv8** algorithm and a **Custom Spatial Scoring Engine**. This project processes digital imagery of road surfaces to detect damage types, measure impacted area ratios, and automatically calculate the Road Damage Index.

---

## 📌 Model Results & Evaluation Summary

The model was trained using the **YOLOv8 Nano** architecture on an **NVIDIA Tesla T4** GPU for 50 epochs (duration: `0.329 hours`).

### 1. Object Detection Evaluation Metrics (Test Set - 199 Images)

| Class | Precision ($P$) | Recall ($R$) | $mAP_{50}$ | $mAP_{50-95}$ | Characteristics / Description |
| --- | --- | --- | --- | --- | --- |
| **All Classes** | **0.587** | **0.504** | **0.516** | **0.226** | Overall aggregate results on test data |
| **Pothole** | 0.527 | 0.447 | 0.426 | 0.160 | Consistently detected across contrast & depth variations |
| **Crack** | 0.467 | 0.393 | 0.381 | 0.148 | High complexity due to thin geometric patterns |
| **Manhole** | **0.767** | **0.673** | **0.742** | **0.369** | Highest performance due to consistent shapes & patterns |

* **Model Weight Size (`best.pt`):** `6.2 MB` (Lightweight & edge-ready).
* **Inference Speed:** **3.9 ms** inference + **1.7 ms** preprocess per image (~178 FPS).

---

## 🏗️ Analysis Pipeline Architecture

The system operates end-to-end through 3 primary stages:

```text
[ Input Road Image ] 
        │
        ▼
[ Stage 1: YOLOv8 Object Detector ] ──> Detect Bounding Box & Class ID
        │
        ▼
[ Stage 2: Spatial Feature Extractor ] ──> Calculate Damaged Area Pixel Ratio (%)
        │
        ▼
[ Stage 3: Custom Scoring Engine ] ──> Compute Penalty & Road Condition Score (0-100)
        │
        ▼
[ Output Report ] ──> Export Results (JSON, CSV, & Visual Annotated Images)
```

---

## ⚖️ Scoring System Logic

The scoring system implements a *Severity Weighting* scheme to apply fair penalty levels based on damage impact on road user safety:

1. **Pothole (Weight: 3.0):** Highest safety hazard risk.
2. **Crack (Weight: 1.5):** Early structural damage.
3. **Manhole (Weight: 0.0):** Normal road component (excluded from penalty).

$$\text{Weighted Damage Ratio (\%)} = \frac{\sum (\text{Bounding Box Area} \times \text{Weight})}{\text{Total Image Area}} \times 100\%$$

$$\text{Road Condition Score} = \max(0, 100 - (\text{Weighted Ratio} \times 12.0))$$

---

## 📊 Sample Analysis Report (Batch Test Set)

Below is an excerpt of actual test results on the test dataset:

| File Name | Total Objects | Damaged Area (%) | Road Score | Penalty | Condition Status | Recommended Action |
| --- | --- | --- | --- | --- | --- | --- |
| `vlcsnap-2025-02-26-20h33m48s935.jpg` | 1 | 0.00% | **100.00** | 0.00 | Good / Very Minor Damage | Routine Maintenance |
| `vlcsnap-2025-02-18-23h18m48s132.jpg` | 0 | 0.00% | **100.00** | 0.00 | Good / Very Minor Damage | Routine Maintenance |
| `vlcsnap-2025-02-18-23h09m10s209.jpg` | 2 | 1.18% | **78.69** | 21.31 | Minor Damage | Periodic Maintenance |
| `vlcsnap-2025-02-18-18h35m54s666.jpg` | 3 | 4.12% | **25.83** | 74.17 | Severe Damage | Full Reconstruction |
| `vlcsnap-00138.jpg` | 3 | 6.38% | **0.00** | 100.00 | Severe Damage | Full Reconstruction |

---

## 📂 Project Directory Structure

```text
ROAD-SENTRY-AI/
├── reports_and_visualizations/      # Directory for generated reports (JSON, CSV, & annotated images)
├── best.pt                          # Best YOLOv8 trained model weights (6.2 MB)
├── data.yaml                        # Class configuration & dataset path for YOLO
├── README.md                        # Primary project documentation
├── requirements.txt                 # Dependencies & Python package list
├── road-sentry-model-training.py    # Main pipeline training & inference script
└── road-sentry-model-training.py.ipynb # Jupyter notebook for execution & experimentation
```

---

## 🚀 Quickstart Guide

### 1. Cloning & Dependency Installation

```bash
git clone https://github.com/zoerlyx/road-sentry-ai.git
cd road-sentry-ai
pip install -r requirements.txt
```

### 2. Start Inference & Training

Run the main Python script:

```bash
python road-sentry-model-training.py
```

Or execute through Jupyter Notebook `road-sentry-model-training.py.ipynb`.

### 3. Quick Model Usage

```python
from ultralytics import YOLO

# Load model weight
model = YOLO('best.pt')

# Analyze road image
results = model('path/to/road_image.jpg')

# Display detection results
for result in results:
    result.show()
```

---

## 💡 Key Project Advantages

* **Ultra Fast Inference:** Processing analysis takes only **~8.4 ms per image**, making it highly suitable for integration into web, mobile, or IoT/Dashcam devices.
* **Objective Assessment:** Converts subjective manual estimation into quantitative scoring based on pixel area percentages.
* **Integration Ready:** Outputs standard structured formats (JSON & CSV) that can directly connect to backend databases or BI dashboards.