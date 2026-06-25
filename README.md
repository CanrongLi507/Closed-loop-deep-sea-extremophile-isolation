# Closed-loop-deep-sea-extremophile-isolation
This repository provides supplementary datasets and source code for the Nature Sensors article “Closed-loop isolation of deep-sea extremophiles through *in* *situ* microenvironments sensing and preserving.” It supports reproduction of the multimodal robotic framework for in situ sensing, preservation, manipulation, and visual servoing.

This repository contains the official implementation of the algorithms described in our manuscript published in Nature Sensors. The system presents a closed-loop robotic framework for automated microbial colony sampling and streaking. It integrates multimodal environmental sensing, adaptive admittance control, and deep learning-based vision.
The codebase is modularized into four independent parts, corresponding to the distinct subsystems of the robotic platform:
>1. **Sensing**: Multimodal fusion and probabilistic trigger mechanisms.
>2. **Streaking**: Trajectory generation and impedance-based contact control.
>3. **Picking**: Visual servoing and PID-driven compliance picking.
>4. **Learning**: Training pipeline for the instance segmentation model (YOLOv11-seg).

## 1. Structure of the series document
### 1.1. Code
The project is organized into four main directories. Each directory contains self-contained modules that map directly to the algorithms presented in the paper

- [Multimodal_Sensing_Triggering/](Multimodal_Sensing_Triggering/): This module implements the environmental perception layer. It fuses data from heterogeneous sensors (fast and slow response), compensates for time delays, and calculates a dynamic score to trigger sampling events.

- [Streaking_Control_System/](Streaking_Control_System/): This module handles the physical interaction between the robot and the agar medium. It generates topological trajectories for streaking and employs an admittance controller to modulate contact forces based on agar concentration.

- [Visual_Perception_Picking/](Visual_Perception_Picking/): This module processes camera input to identify pickable colonies. It performs optical calibration, parses segmentation masks, evaluates colony quality (features), and executes a PID-controlled picking sequence.

- [Model_Training/](Model_Training/): This script handles the training of the YOLOv11-seg model used in Module 3. It is configured for high-resolution input to detect small colonies.

### 1.2. Additional Information

This repository includes the primary datasets supporting the machine learning training and experimental validation described in the manuscript. The data is organized into two main components:

A. Visual Dataset for Deep Learning
- `YOLO_Training_Dataset.zip/`: A comprehensive collection of approximately ~5,000 high-resolution images capturing microbial plate streaking morphologies on various agar substrates. This dataset was utilized to train, validate, and test the vision-guided robotic system (YOLOv11-seg) for colony instance segmentation. Raw optical images (.jpg, .png) encompassing a wide range of experimental conditions, including dense colony clusters, blurred or overlapping boundaries, and micro-scale colonies.

B. Integrated Experimental data Logs
- `Supplementary_Data_Table.xlsx/`: An aggregated spreadsheet containing source data for robotic performance, environmental sensing, and microbiological analysis. It is structured into ten specific worksheets:
>1. **Sheets 1-2 (Robotic Validation)**: Haptic feedback logs (𝐹<sub>𝑧</sub>, 𝐹<sub>𝑡</sub>) and kinematic positioning error statistics.
>2. **Sheets 3-4 (*In* *Situ* Sensing)**: Real-time physicochemical profiles (CH<sub>4</sub>, DO, CO<sub>2</sub>) and sampler hydrostatic pressure logs.
>3. **Sheet 5 (Incubation Metrics)**: Temperature/pressure stability data and retention efficiency calculations.
>4. **Sheets 6-7 (Microbial Community)**: Taxonomic abundance tables (Phylum level) and Z-score normalized genera distributions.
>5. **Sheets 8-9 (Genomic Analysis)**: Genome cohort metadata and functional gene annotations for deep-sea isolates HP_SR9/HP_SR10.
>6. **Sheet 10 (High-pressure Isolate Inventory)**: Inventory of prokaryotic isolates obtained under high‑pressure in situ conditions using the DISCUSS system.


### 1.3. Data sources

The proposed closed-loop system integrates **heterogeneous data streams** spanning chemical sensing, physical interaction, robotic motion, vision perception, and learning-based annotation. All data sources are explicitly modeled to preserve **physical causality**, **temporal consistency**, and **experimental reproducibility**, ensuring that no algorithmic component relies on circular or post-hoc information.

A. Visual Perception Dataset (Deep Learning)
- This dataset underpins the Module 4 (Learning) and Module 3 (Picking) components. It was constructed to train the YOLOv11-seg model for robust microbial colony instance segmentation under varying illumination and agar conditions.
The Visual Perception Dataset was constructed using high-fidelity images acquired from the system's on-board Sony IMX series industrial camera, integrated directly with the VisualCalibration module detailed in Part 3 of the codebase. Encapsulated within YOLO_Training_Dataset.zip, this collection comprises approximately ~5,000 samples that have been resized to 1280×1280 pixels to align with the imgsz = 1280 hyperparameter defined in the train_segmentation.py script. To ensure robust model generalization, the dataset encompasses five distinct agar substrates (including R2A, ISP2, postgate, TCBS, GSM-1, and Marine Agar 2216) and covers a wide spectrum of colony densities ranging from sparse distribution to confluent growth. Ground truth labels were generated using semi-automated polygon annotation tools with subsequent rigorous verification by expert microbiologists, and the final dataset is structured according to the standard YOLO Segmentation format to facilitate immediate integration into the training pipeline.

B. Sensorimotor & Environmental Telemetry
- This dataset corresponds to the raw log files generated during the execution of Module 1 (Sensing) and Module 2 (Streaking). It allows researchers to reproduce the signal processing and control logic simulations.
The Sensorimotor & Environmental Telemetry dataset is consolidated within Supplementary_Data_Table.xlsx (Sheets 1-4), providing high-resolution logs essential for reproducing the system's control and sensing logic. Sheets 1-2 contain Haptic Interaction Logs recorded at 100 Hz during the streaking process, capturing critical time-series variables including timestamp_ms, measured_force_N (𝐹<sub>𝑧</sub>), commanded_velocity_mm_s (𝑣<sub>𝑧</sub>), and deformation_depth_mm (𝑧<sub>𝑐𝑚𝑑</sub>​); these metrics are utilized to validate the stability of the Admittance Controller (Algorithm 3) and the rupture-prevention logic of the Safety Manager. Complementing this, Sheets 3-4 present Multimodal Sensor Fusion data sampled at 10 Hz, detailing both raw voltages and calibrated readings for the environmental array (CH<sub>4</sub>, CO<sub>2</sub>, DO, Temperature). Crucially, these logs include paired 'Fast' and 'Slow' sensor streams, which serve as the ground truth for evaluating the time-delay rectification performance of the Dynamic Compensator (Algorithm 1) and the noise suppression of the Adaptive Kalman Filter.

C. Biological & Genomic Validation
- This section provides the downstream biological analysis results, confirming that the robotic sampling (Algorithm 4) and streaking (Algorithm 3) maintained sample integrity and viability.
The Biological & Genomic Validation dataset is archived in Sheets 6-10 of Supplementary_Data_Table.xlsx, providing critical downstream evidence of the system's sampling integrity. Sheets 6-7 detail the Microbial Community Structure based on 16S rRNA gene sequencing, offering a comparative analysis between manual and robotic sampling methodologies to verify community retention. Furthermore, the dataset includes comprehensive Genomic Metadata—specifically assembly statistics such as N50, contig counts, and completeness scores—for the deep-sea isolates HP_SR9 and HP_SR10. These records serve to biologically validate the control logic, demonstrating that the Regime-Aware Trigger (Algorithm 2) successfully identified and acted upon biologically significant phenomena, such as plume signals, resulting in the precise isolation of the target species.

## 2. Using Code Ocean

1. Clone or download the repository from Zenodo.
2. Install required dependencies (NumPy, SciPy, OpenCV, PyTorch, Ultralytics).
3. Execute each module independently for testing, or integrate them sequentially for full closed-loop simulation:
   - Part 1 → sensing & triggering  
   - Part 2 → compliant streaking  
   - Part 3 → colony perception & picking  
   - Part 4 → segmentation model training/inference  

Each folder contains runnable examples illustrating typical usage scenarios.

# Citation

If you utilize the high-resolution microbial colony dataset, the comprehensive *in* *situ*-to-laboratory transfer data logs, or the robotic execution algorithms provided in this repository for your research, please cite the following publication:
1. J.-C. Feng. Closed-loop isolation of deep-sea extremophiles through *in* *situ* microenvironments sensing and preserving. *Nat.* *Sens.*, 2025.
2. J.-C. Feng. Closed-loop isolation of deep-sea extremophiles through in situ microenvironments sensing and preserving [Data set]. Zenodo. https://doi.org/10.5281/zenodo.18442830.
