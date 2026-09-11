# Amodal Instance Segmentation Pipeline for Mealworm Growth Monitoring

> **Repository Notice:** The core source code, model weights, and underlying datasets for this pipeline are proprietary assets maintained within a private institutional repository for laboratory handoff and ongoing research. This public workspace serves as an architectural blueprint, deployment specification, and visual portfolio showcasing the system's engineering methodology and performance results.

This repository houses the computer vision and automated data processing pipeline for larval growth tracking of the mealworm (*Tenebrio molitor*). Industrial insect farming requires transitioning from manual handling to automated robotic rearing systems driven by computer vision.

A critical bottleneck is larval growth tracking to optimize feeding and predict harvest readiness. Traditional computer vision metrics fail in high-density farm environments because larvae heavily cluster and occlude one another. This project advances the amodal framework by developing a computer vision pipeline prioritizing larval width measurement over length, as body width remains visible under occlusion to provide a robust metric for developmental instar classification.

---

## System Architecture & Methodology

The software framework executes across four primary tasks:

```
┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐     ┌─────────────────────────┐
│ 1. Instance Extraction  │ ──> │ 2. Synthetic Generation │ ──> │ 3. Length Measuring     │ ──> │ 4. Chart & Evaluation   │
│  - HSV/BGR Segmentation │     │  - Cluster Compositing  │     │  - Skeletonization      │     │  - Growth Tracking      │
│  - Isolate Clean Larvae │     │  - AISFormer Training   │     │  - Z-Score Filtering    │     │  - Mean Absolute Error  │
└─────────────────────────┘     └─────────────────────────┘     └─────────────────────────┘     └─────────────────────────┘
```

---

## Comprehensive Pipeline & Implementation Details

The software framework is executed sequentially across the following six detailed engineering phases:

### 1. Instance Extraction & Clean Views

### Objective

Isolate high-fidelity, non-occluded larval instances to serve as the baseline assets for downstream synthetic data augmentation. This stage has two scripts: `crop_background.py` prepares clean empty-tray background images, and `extract_instances.py` segments and extracts individual larval instances as clean RGBA crops.

### Implementation

Raw overhead images of sparse trays are processed using a localized thresholding pipeline. Color-space transformations (converting BGR to HSV) isolate the unique color profile of the larvae from the background grid. Contours are filtered by area to discard background debris, and each surviving instance is refined through an erosion/smoothing pipeline before being extracted as a clean, individual larval mask.

---

### Part A — Background Cropping (`crop_background.py`)

Raw background tray photos include silver tray edges/frame that need to be trimmed away before the image is usable as a synthetic-scene background. This script center-crops each raw background image to a fixed rectangle, offset if needed.

**Key Script:** `/src/crop_background.py`

---

### Part B — Larval Instance Extraction (`extract_instances.py`)

#### 1. Color-Based Rough Segmentation
The HSV saturation channel isolates the larvae's color profile from the tray background, followed by a morphological closing pass to fill small internal holes and reconnect any broken parts of a worm's mask.

#### 2. Contour Detection & Area Filtering
Individual larvae are found as external contours; anything under an area threshold is discarded as background noise/debris.

#### 3. Baseline + Adaptive Erosion
Every surviving instance is eroded once as a baseline tightening pass. A curvature check on the mask's contour then detects sharp surviving artifacts (jagged spikes from adjacent, touching debris) and applies extra erosion iterations only where needed.

**Key Script:** `/src/extract_instances.py`

---

### 2. Synthetic Scene Generation
### Objective

Build a **procedural synthetic-data generation pipeline** that creates high-density mealworm scenes and automatically produces **amodal, visible, and occlusion annotations**, reducing the need for manual labeling.

### Implementation

The pipeline is implemented in **Python using OpenCV, NumPy, and COCO-style JSON annotations**. Extracted mealworm instances from Phase 1 are treated as reusable image assets and procedurally assembled onto real empty-tray backgrounds.

Each synthetic image is generated through the following pipeline: 

#### 1. Larval Instance Transformation
Individual mealworm PNGs are randomly sampled and transformed using:
- Random flipping
- Brightness scaling
- HSV hue and saturation variation
- Gaussian-distributed scaling
- Random rotation from 0°–360°

#### 2. Procedural Spatial Distribution
To reproduce the non-uniform distribution of real mealworm populations, the generator creates **2–4 spatial cluster centers** and samples most larvae from Gaussian distributions around these centers.
This produces naturally concentrated groups rather than uniformly distributed objects across the tray.

#### 3. Controlled Density and Occlusion
Each difficulty tier specifies a target larval count and maximum allowable overlap.
For every candidate larva, the generator compares its binary mask against a global instance-ID mask.

<div align="center">

| Dataset Tier | Train Images | Validation Images | Larvae Density (Count) | Max Occlusion ($o_{\text{max}}$) |
| :--- | :---: | :---: | :---: | :---: |
| **Easy** | 800 | 200 | 60 | 15% |
| **Medium** | 800 | 200 | 90 | 25% |
| **Hard** | 800 | 200 | 120 | 45% |

</div>

#### 4. Amodal Mask Generation
Before occlusion occurs, the complete transformed larval mask is stored as an amodal mask, representing the full body shape.
A global instance-ID map is simultaneously maintained to track the visible pixels belonging to each individual larva.

#### 5. Shadow and Alpha Blending
Each larva is composited onto the tray using alpha blending rather than directly pasting pixels.

#### 6. Procedural Burrowing
To simulate larvae partially obscured by substrate, the generator creates a random noise field and converts it into localized burrowing regions.
The corresponding pixels are removed from the larval mask so that the generated image and segmentation annotations remain synchronized.

#### 7. Automatic Occlusion Mapping
After all larvae have been placed, the global instance-ID mask is used to reconstruct the visible mask of every individual larva.
The occluded region is calculated using a pixel-wise XOR between the amodal and visible masks.

#### 8. COCO Polygon Generation
Binary masks are converted into polygon representations using OpenCV contour extraction

#### 9. Camera and Imaging Simulation
To reduce the domain gap between synthetic and real images, randomized camera-level effects are applied to the completed scene

**Key Scripts:** `/src/generate_amodal_dataset.py`

---

### 3. Train Amodal Instance Segmentation (AIS) Model
### Objective

Train an **AISFormer amodal instance segmentation model** on the procedurally generated mealworm dataset from Phase 2.

The training pipeline is built on **Detectron2** and configures AISFormer for single-class mealworm segmentation, with separate training and validation datasets registered in COCO format.

### Training Configuration Summary

| Component | Configuration |
|---|---|
| Framework | Detectron2 |
| Architecture | AISFormer |
| Backbone | ResNet-50 + FPN |
| Backbone Weights | ImageNet Pre-trained |
| Task | Amodal Instance Segmentation |
| Classes | 1 — Mealworm |
| Optimizer | AdamW |
| Initial Learning Rate | 0.005 |
| Warm-up | 1,000 iterations |
| Total Training | 60,000 iterations |
| LR Decay | 42,000 / 56,400 iterations |
| LR Decay Factor | 0.1 |
| Batch Size | 2 images |
| RoIs / Image | 512 |
| AMP | Enabled |
| DataLoader Workers | 12 |
| Training Resolution | 640–800 px minimum side |
| Maximum Resolution | 1333 px |
| Evaluation | COCOEvaluator |
| Checkpoint Period | 1,000 iterations |
| GPU | NVIDIA RTX 4090 24 GB |

### Training Workflow

The complete training pipeline can be summarized as:

```
Synthetic COCO Dataset
        │
        ▼
Detectron2 Dataset Registration
        │
        ▼
AISFormer + ResNet-50 FPN
        │
        ▼
Amodal Segmentation Configuration
        │
        ├── AdamW Optimizer
        ├── 60,000 Iterations
        ├── Multi-Scale Training
        ├── Automatic Mixed Precision
        └── 4 Query Embeddings
        │
        ▼
GPU Training
        │
        ▼
Checkpoint Management
        │
        ▼
Final AISFormer Model
```

**Key Scripts:** `/src/train_mealworms_amodal.py`

---

### 4. Length Measurement — Results Logging & Visualization
### Objective

For each processed image, take the per-instance classification results and the population-level length statistics, then **draw a debug overlay, log every instance to CSV, and write a per-image summary** — before releasing GPU memory and moving to the next image.

### Pipeline
#### 1. Population-Level Length Stats
Length statistics are computed only from instances that passed the full filter chain (`reason == "valid"`), and only if a calibration value exists for that image's tray.

#### 2. Debug Overlay Drawing
Every instance — valid or rejected — gets its contour drawn on the overlay image in its reason's assigned color. Valid instances are additionally filled with a semi-transparent tint, and labeled with their measured length in millimeters.

#### 3. Per-Instance CSV Logging
Every instance — regardless of whether it passed or failed the filter chain — is written as one row to `instance_filter_stats.csv`, preserving the full geometric and shape-diagnostic feature set alongside the final length measurement.

The CSV is flushed to disk after every image, so a crash mid-batch doesn't lose already-processed results.

| Column | Description |
|---|---|
| `image` | Source filename |
| `instance_idx` | Detection index within the image |
| `reason` | `valid`, or the specific filter-chain rejection reason |
| `area` | Instance mask area in pixels |
| `num_components` | Connected components found for this instance's mask |
| `fragment_extra_area` | Area belonging to non-primary components |
| `extent` | Mask area / rotated bounding box area |
| `aspect_ratio` | Bounding box major / minor axis ratio |
| `elongation` | Skeleton arc length / average width |
| `skeleton_length` | Pruned-skeleton tip-to-tip arc length (px) — the length measurement itself |
| `off_path_px` | Skeleton pixels off the main tip-to-tip path |
| `off_path_width_ratio` | Off-path pixels normalized by average width |
| `head_to_tail_length` | Straight-line endpoint distance, used in cluster recovery |
| `length_width_ratio` | Head-to-tail length / estimated width, used in cluster recovery |
| `color_gradient` | Mean intensity change along the skeleton, used in cluster recovery |
| `visibility_ratio` | Visible pixels / amodal area |
| `visible_pixels` | Raw visible pixel count |
| `brightness` | Mean grayscale brightness of the instance |
| `length_mm` | Final calibrated length, if measured |
| `length_outlier` | Whether this instance was excluded as a population outlier |

#### 4. Console and Per-Image Summary
A one-line breakdown of valid vs. rejected instance counts is printed for every image
If length statistics were computed, a second summary line reports the measured/outlier counts and distribution, and a row is appended to `length_summary.csv`.

#### 5. Debug Image Output
The annotated overlay for the image is written to disk, named by tray folder and file stem so results from different trays never collide.

#### 6. GPU/Memory Cleanup
Large per-image tensors and arrays are explicitly deleted and the CUDA cache is cleared before moving to the next image, keeping VRAM usage flat across a long batch run.

#### 7. Batch Completion
Once every image has been processed, both CSV files are closed and the length-distribution charts are generated from the accumulated per-instance data.

### Output Files

| Output | Description |
|---|---|
| `instance_filter_stats.csv` | Every detected instance, one row each, with full geometric/shape features and filter outcome |
| `length_summary.csv` | One row per image: measured count, outlier count, mean/median/std/min/max length, dominant growth stage, harvest readiness |
| `filter_length_debug_<tray>_<image>.png` | Annotated debug image with per-instance contours and length labels |
| `length_chart_<image>.png` | Length distribution histogram + instance-breakdown bar chart (generated separately, see chart generation section) |

**Key Script:** `/src/length_measure.py`

---

### 5. Growth Inference Chart
### Objective

Fit a **Gompertz growth curve** to measured mealworm width data over time, producing a validation chart that shows the population's growth trend and (optionally) predicts the optimal harvest day for a target width.

### Implementation

The script is implemented in **Python using NumPy, SciPy, pandas, and Matplotlib**. It reads per-instance width measurements from a CSV, averages them by day, fits a Gompertz model via non-linear regression, and renders the result as a chart.

### Important Note

Environmental conditions vary significantly across different trays, including lighting, feeding, watering, and humidity levels. These variations cause an uneven length distribution for different trays across ages. Consequently, the length vs. time graph may not display a traditional smooth growth curve. 

Overall, the ultimate goal of this project is to evaluate the model's ability to predict worm length across different ages, which will be formally evaluated in **Stage 6**.

### Running

By default (`show_harvest=False`), the script writes `growth_inference_chart_wo_harvest.png` to the configured output directory without harvest-day graphics; setting `show_harvest=True` in the `generate_validation_chart()` call adds the target-width line, predicted-day marker, and annotation.

**Key Script:** `/src/length_chart_building.py`

---

### 6. Ground-Truth Length Measurement & Pipeline Accuracy Evaluation
### Objective

Establish an independent, non-AISFormer **ground-truth measurement pipeline** for mealworm length (and width, as secondary reference), then use its output to **quantify how closely the AISFormer pipeline's measurements track ground truth** — reporting error metrics (MAE, RMSE, percentage accuracy), a per-day residuals CSV, and comparison charts.

This stage has two scripts: `gt_length_measure.py` generates the ground-truth measurements directly from clean, non-occlusion tray photos using classical CV (no AISFormer/Detectron2 involved), and `length_evaluate_pipeline.py` compares those ground-truth numbers against the AISFormer pipeline's own measurements.

---

### Part A — Ground-Truth Length Measurement (`gt_length_measure.py`)

This is a **length-primary variant** of the project's earlier ground-truth width script. The pruned-skeleton tip-to-tip arc length — previously only used as a minimum-length filter and an overlay label — now drives outlier filtering, per-image summary stats, and the distribution chart. Width is still measured with the same perpendicular-marching algorithm as before and kept in the CSV/overlay as secondary reference info.

---

### Part B — Pipeline Accuracy Evaluation (`length_evaluate_pipeline.py`)
Takes the ground-truth measurements produced above alongside the AISFormer pipeline's own measurements (paired by day) and quantifies agreement between the two.


---

## System Layout & Components

```text
├── models/                            
│   └── model_final.pth                # Optimized transformer weights after 60k iterations (Phase 3)
├── output_visuals/
│   ├── inference_result/              # Visualization of length measurement outputs (Phase 4)
│   ├── charts/                        # Longitudinal Gompertz growth tracking charts (Phase 5)
│   └── evaluation/                    # Model performance metrics (MAE/RMSE logs, residual plots, and error matrices) (Phase 6)
├── data/               
│   ├── raw/
│   │   ├── background/                # Pure tray backgrounds containing only substrate
│   │   └── instance/                  # Baseline photos of larvae under non-occluded conditions
│   ├── extracted/
│   │   ├── train_instance/            # Clean, isolated larval masks extracted in Phase 1 for training dataset in Phase 2
│   │   ├── val_instance/              # Clean, isolated larval masks extracted in Phase 1 for validation dataset in Phase 2
│   │   └── cropped_background/        # Clean, cropped background extracted in Phase 1
│   ├── extracted_clutter/
│   │   ├── beetles/                   # Clean, isolated larval masks of beetles from online datasets
│   │   ├── pupae/                     # Clean, isolated larval masks of pupae from online datasets
│   │   └── substrate_flakes/          # Clean, isolated larval masks of substrate_flakes from online datasets
│   ├── synthetic/                    
│   │   ├── train_set/                
│   │   │   ├── train_img/             # Augmented high-density training dataset generated in Phase 2 (not presented in this repository due to space limitations)
│   │   │   └── annotations_train.json # Annotation of the training dataset generated in Phase 2 (not presented in this repository due to space limitations)
│   │   └── val_set/                  
│   │       ├── val_img/               # Augmented high-density validation dataset generated in Phase 2 (not presented in this repository due to space limitations)
│   │       └── annotations_val.json   # Annotation of the validation dataset generated in Phase 2 (not presented in this repository due to space limitations)
│   └── inference/                     # Real-world production tray images for model testing
├── src/                
│   ├── crop_background.py             # Phase 1: Background isolation
│   ├── extract_instances.py           # Phase 1: Instance extraction & HSV thresholding
│   ├── generate_amodal_dataset.py     # Phase 2: Synthetic dataset generation and COCO annotation
│   ├── train_mealworms_amodal.py      # Phase 3: AISFormer deep learning model training
│   ├── length_measure.py              # Phase 4: Inference, skeletonization, and z-score filtering
│   ├── length_chart_building.py       # Phase 5: Non-linear regression and predictive modeling
│   ├── gt_length_measure.py           # Phase 6: Ground truth measurement
│   └── length_evaluate_pipeline.py    # Phase 6: MAE evaluation and accuracy benchmarking
├── requirements.txt                   # Python package dependencies
└── docs/
    ├── stage1_extraction.png
    ├── background.png
    ├── synthetic_old.png
    ├── synthetic_updated.png
    ├── synthetic_annotations.png
    ├── length_chart.png
    ├── growth_curve.png
    ├── gt_length_inference.png
    ├── gt_length_chart.png
    ├── mae_residual.png
    ├── raw_topview.png
    └── length_inference.png
```

---

## System Requirements & Core Dependencies

The underlying algorithmic pipeline is designed to deploy within a containerized Linux ecosystem optimized for CUDA-accelerated computer vision tasks. 

### Core Specifications
* **Operating System:** Linux (Ubuntu 20.04 / 22.04 LTS verified)
* **Hardware Acceleration:** NVIDIA GPU (CUDA Compute Capability 8.0+ recommended)
* **Environment Runtime:** Python 3.8+ / Virtualenv

### Key Frameworks & Dependencies
The software stack relies on the following primary open-source distributions (detailed specifications are mapped in `requirements.txt`).

---

## Evaluation & Metrics

To validate the computer vision system’s estimates under high-density occlusion, absolute physical ground-truth measurements are established by capturing separate, non-occluded images of individual larvae alongside a spatial calibration grid ruler. 

Overall model length accuracy is quantified using two primary metrics across sampled validation cohorts:

### 1. Mean Absolute Error (MAE)
$$\text{MAE} = \frac{1}{n} \sum_{i=1}^{n} |L_{\text{predicted}, i} - L_{\text{ground truth}, i}|$$

Where $L_{\text{predicted}}$ and $L_{\text{ground truth}}$ represent the predicted and absolute physical larval lengths in millimeters, respectively. MAE provides a direct measure of physical error magnitude across the dataset.

### 2. Length Accuracy Percentage
$$\text{Accuracy (in percentage)} = \left( 1 - \frac{1}{n} \sum_{i=1}^{n} \frac{|L_{\text{predicted}, i} - L_{\text{ground truth}, i}|}{L_{\text{ground truth}, i}} \right) \times 100$$

This percentage metric reflects the overall fidelity of the model's spatial predictions relative to the physical scale of individual larvae across varying growth stages.

By accurately tracking these error margins, the pipeline continuously filters tracking anomalies, extracts pure inlier growth trends, classifies biological instar stages, and constructs reliable longitudinal growth curves for farm optimization.

---

## Inference Result

Here is an example of how the model would visualize the result (Phase 4). Before running the code (`/src/length_measure.py`), make sure to change to the correct calibration value in *mm/pixel*. 

<table>
  <tr>
    <td length="50%" align="center" valign="bottom">
      <img src="https://github.com/anshenglu2019-design/biomass-cv-pipeline/blob/main/docs/3_9.jpg" height="400" alt="Raw Mealworm Input Image">
      <br>
      <em>Figure 10: Raw overhead camera capture of mealworm biomass data collection.</em>
    </td>
    <td length="50%" align="center" valign="bottom">
      <img src="https://github.com/anshenglu2019-design/biomass-cv-pipeline/blob/main/docs/filter_length_debug_7_28_3_9.png" height="400" alt="ML Inference Result">
      <br>
      <em>Figure 11: Inference showing worm detection and length measurement in dense scenes.</em>
    </td>
  </tr>
</table>

---

<div class="references-section">
        <h2> References</h2>
        
 <div class="reference-item">
            [1] Nawoya, S., et al. (2024). Computer vision and deep learning in insects for food and feed production: A review. <i>Computers and Electronics in Agriculture</i>, 216, 108503.
        </div>
        
 <div class="reference-item">
            [2] Dolata, P., et al. (2025). Amodal Instance Segmentation for Mealworm Growth Monitoring Using Synthetic Training Images. <i>IEEE Access</i>, 13, 52157-52175.
        </div>
    </div>

