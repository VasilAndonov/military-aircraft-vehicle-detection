## Title
Comparative Analysis of One-Stage (YOLOv8) and Two-Stage (Faster R-CNN) Detectors in Remote Sensing Images

## Overview
The study aims to quantify performance differences between two dominant object detection paradigms—one‑stage (YOLOv8n) and two‑stage (Faster R‑CNN with MobileNetV3‑FPN)—when applied to aerial imagery. Both models are trained and evaluated on two independent datasets: the **MVRSD‑Aerial dataset** (3 001 images, 5 vehicle classes) and the **Military‑Aircraft‑Recognition dataset** (3 842 images, 20 aircraft classes). Three specific hypotheses are tested, covering the role of background complexity, localisation precision under strict IoU thresholds, and the effect of object‑scale variation on recall. The study is a 2 × 2 factorial simulation: Architecture (YOLOv8 vs. Faster R‑CNN) × Dataset (MVRSD vehicles vs. Aircraft).

## Repository Structure and Data Availability
The complete code, results, and supplementary materials are distributed across two locations for convenience and size management.

### GitHub Repository
Theoretical notebook, article notebook and key visualisations are available in this GitHub repository:  
[https://github.com/VasilAndonov/military-aircraft-vehicle-detection](https://github.com/VasilAndonov/military-aircraft-vehicle-detection)

The repository contains the following files:

```text
military-aircraft-vehicle-detection/
├── README.md
├── requirements.txt
├── article.ipynb
├── theoretical-background.ipynb
├── plots/
│ ├── class-dist-MA.png
│ ├── class-dist-MV.png
│ ├── color-dist.png
│ ├── conf-matrix-norm-MA.png
│ ├── conf-matrix-norm-MV.png
│ ├── PR-MA.png
│ └── PR-MV.png
```
- `requirements.txt` – List of required Python packages to reproduce the environment;
- `article.ipynb` – The main Jupyter notebook containing the full article text;
- `theoretical-background.ipynb` – A notebook covering the theoretical foundations of the metrics and architectures employed;
- `plots/` – Directory containing all figures referenced in the article.

### Google Drive
Because the raw datasets and trained model weights are large, they are provided via a Google Drive folder:  
[https://drive.google.com/drive/folders/1mCQIK2ASnt_bOn1L8f64in64ht-Z4WNf?usp=sharing](https://drive.google.com/drive/folders/1mCQIK2ASnt_bOn1L8f64in64ht-Z4WNf?usp=sharing)

The Drive folder is organised as follows:

```text
military-aircraft-vehicle-detection/
├── data/
│ ├── EDA.ipynb
│ ├── MVRSD-Aerial-dataset/
│ └── Military-Aircraft-Recognition-dataset/
├── YOLO-MV/
│ ├── yolo-mv.ipynb
│ ├── yolov8n.pt
│ ├── MVRSD_yolo/
│ └── runs/detect/MVRSD_yolov8/
├── YOLO-MA/
│ ├── yolo-ma.ipynb
│ ├── yolov8n.pt
│ ├── Aircraft_yolo/
│ └── runs/detect/Aircraft_yolov8/
├── Faster-R-CNN-MV/
│ ├── faster-r-cnn-mv.ipynb
│ └── mvrsd_faster_rcnn_final.pth
└── Faster-R-CNN-MA/
├── faster-r-cnn-ma.ipynb
└── best_faster_rcnn_mobilenet.pth
```

- `data/` – Contains the two raw datasets (`MVRSD-Aerial-dataset` and `Military-Aircraft-Recognition-dataset`) alongside an exploratory data analysis notebook (`EDA.ipynb`) that generates the class distribution, colour distribution, entropy, and edge density statistics.
- `YOLO-MV/` – YOLOv8 training notebook for the MVRSD vehicle dataset, along with the YOLO‑formatted dataset copy, the base weights (`yolov8n.pt`), and the `runs/` folder containing evaluation outputs (confusion matrices, PR curves, F1 curves, batch prediction visualisations).
- `YOLO-MA/` – YOLOv8 training notebook for the Military Aircraft dataset, with analogous outputs.
- `Faster-R-CNN-MV/` – Faster R‑CNN training notebook for the MVRSD dataset, plus the saved model checkpoint (`mvrsd_faster_rcnn_final.pth`).
- `Faster-R-CNN-MA/` – Faster R‑CNN training notebook for the Aircraft dataset, with the best model checkpoint (`best_faster_rcnn_mobilenet.pth`).

Each model folder is self‑contained and can be executed independently after placing the corresponding dataset in the expected path. All notebooks include fully executable cells. The YOLOv8 base weights (`yolov8n.pt`) are downloaded automatically by the Ultralytics library when first referenced; copies are also placed in the YOLO‑MV and YOLO‑MA folders for offline use.

## Hypotheses
1. **Background complexity and false positives**  
   - **H0:** There is no difference in spatial entropy and Canny edge density between the MVRSD‑Aerial and Military‑Aircraft‑Recognition datasets.  
   - **H1:** The MVRSD‑Aerial dataset exhibits higher spatial entropy and edge density, predisposing detectors to a higher rate of false positives (and missed detections) compared to the aircraft dataset.

2. **Localisation precision**  
   - **H0:** There is no difference in strict‑IoU localisation accuracy (mAP @ [.75:.95]) between YOLOv8 and Faster R‑CNN.  
   - **H1:** Faster R‑CNN, by virtue of its RoI Align layer, achieves superior localisation accuracy for small objects compared to YOLOv8, whose grid‑based structure loses spatial information during down‑sampling.

3. **Scale variance and recall**  
   - **H0:** There is no significant difference in recall performance between the two datasets.  
   - **H1:** Both models show lower recall on the aircraft dataset due to a larger coefficient of variation (CV) of object areas (ranging from small fighters to large transport aircraft), compared to the relatively standardised dimensions of ground vehicles.

## Study Type
Simulation study – controlled training and evaluation of object detection models on fixed datasets.

## Study Design
2 × 2 factorial design:  
- Factor A: Detector architecture (YOLOv8n vs. Faster R‑CNN with MobileNetV3‑FPN backbone).  
- Factor B: Dataset (MVRSD‑Aerial, 5 classes vs. Military‑Aircraft‑Recognition, 20 classes).  

Each combination is trained once with a fixed random seed (42). Final evaluation is performed on the official validation split of each dataset using COCO‑style metrics. No cross‑validation is applied.

## Data Collection Procedures
- **MVRSD‑Aerial dataset:** downloaded from Kaggle. Annotations are in YOLO format. Official train (2 400 images) / validation (600 images) split is used.  
- **Military‑Aircraft‑Recognition dataset:** downloaded from Roboflow. Annotations in Pascal‑VOC XML format (horizontal bounding boxes only). The split defined in `ImageSets/Main/train.txt` and `test.txt` is used, with the test set treated as the validation set.  
- All images are resized to 640 × 640 pixels before training. No other preprocessing or clean‑up is applied.  
- Image‑level statistics (Shannon entropy, Canny edge density) are computed on the combined training + validation images of each dataset.

## Sample Size
- MVRSD‑Aerial: 3 001 total images (2 400 train / 601 val).  
- Military‑Aircraft‑Recognition: 3 842 total images (2 563 train / 1 279 val, as derived from the official splits).  
- Per‑class instance counts are documented in the exploratory data analysis (see class distribution charts in the repository).

## Starting and Stopping Rules
- **YOLOv8:** Training starts from COCO‑pretrained `yolov8n.pt` weights. Maximum 80 epochs, early stopping with patience of 15 epochs monitoring validation mAP (built‑in, though validation during training is disabled; see below). Training is performed once per dataset.  
- **Faster R‑CNN:** Training starts from COCO‑pretrained `fasterrcnn_mobilenet_v3_large_fpn` weights. 20 epochs, with learning rate drops at epochs 12 and 18 (factor 0.1). Training is performed once per dataset.  
- No hyperparameter tuning is performed. All runs use the same random seed (42).

## Manipulated Variables
- Detector architecture (YOLOv8n vs. Faster R‑CNN).  
- Dataset (MVRSD‑Aerial vs. Military‑Aircraft‑Recognition).  
All other training hyperparameters (batch size, learning rate, augmentation) are fixed per architecture.

## Measured Variables
- **Dataset complexity metrics:** Shannon entropy (average per image), Canny edge density (percentage of edge pixels per image).  
- **Detection performance metrics (COCO):**  
  - mAP @ [.50:.95] (primary precision measure)  
  - mAP @ [.75:.95] (strict localisation)  
  - AP @ 0.50, AP @ 0.75  
  - Average Recall (AR) @ maxDets=100, and AR broken down by object size (small: area < 32² px, medium: 32² ≤ area < 96² px, large: area ≥ 96² px)  
- **Class‑wise stability:** Coefficient of Variation (CV) of per‑class AP across all IoU thresholds.  
- **Qualitative diagnostics:** Normalised confusion matrices (row‑wise recall) and precision‑recall curves.

## Statistical Models
No inferential statistical tests are performed. The study is a descriptive comparison. Effect sizes are reported as raw differences in mAP and recall between architectures and datasets. All metrics are computed on a single validation run (no bootstrapping or confidence intervals).

## Inference Criteria
Hypotheses are evaluated based on the direction and magnitude of the observed metrics:
- **H1:** Supported if MVRSD shows higher entropy and edge density **and** leads to lower precision / higher background confusion.  
- **H2:** Supported if Faster R‑CNN yields higher mAP @ [.75:.95] than YOLOv8, especially on small objects.  
- **H3:** Supported if recall on the aircraft dataset is lower than on MVRSD, and the difference is more pronounced for YOLOv8.  

No p‑values are reported; interpretation relies on the practical significance of the metric differences.

## Data Inclusion and Exclusion
- All images provided in the official dataset releases are used. No images are excluded based on quality, occlusion, or label ambiguity.  
- Validation is performed **after** training in a separate step. For YOLOv8, built‑in validation during training is disabled to avoid NMS time‑limit warnings; the NMS time limit is set to 10 seconds for the final validation pass.  
- The `test` subfolder in MVRSD (containing large TIFF files) is excluded from training/validation and is not evaluated.

## Missing Data
No missing data are present in either dataset. All images have corresponding annotations. No imputation is required.

## Context and Additional Information
- Training hardware: MacBook Pro M1 16G RAM (2020).  
- Code is provided in Jupyter notebooks within this repository. A `requirements.txt` file lists all necessary dependencies.  
- Pre‑trained weights are not included due to size, but training scripts are fully reproducible.
 include fully executable cells. The YOLOv8 base weights (`yolov8n.pt`) are downloaded automatically by the Ultralytics library when first referenced; copies are also placed in the YOLO‑MV and YOLO‑MA folders for offline use.
