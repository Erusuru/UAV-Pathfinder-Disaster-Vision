# UAV-Pathfinder: Aerial Vision for Post-Disaster Road & Debris Segmentation

[![Domain](https://img.shields.io/badge/domain-UAV%20Aerial%20Vision%20%7C%20Disaster%20Response-blue.svg)](#why-this-project)
[![Pipeline](https://img.shields.io/badge/pipeline-16--Stage%20Deterministic%20CV-orange.svg)](#16-stage-deterministic-vision-pipeline)
[![Target Platform](https://img.shields.io/badge/target-Raspberry%20Pi%205%20%7C%20Embedded-red.svg)](#technical-specifications--embedded-benchmarks)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](#license)

**Author:** Ramazan Ertuğrul Aydoğan  
**Affiliation:** *Department of Electrical and Electronics Engineering, Gaziantep University (GAÜN), Gaziantep, Türkiye*  
**Academic Focus:** *Aerial Robotics, Embedded Computer Vision, Search & Rescue (SAR) Autonomy*

---

## Table of Contents

- [Why This Project](#why-this-project)
- [System Overview](#system-overview)
- [16-Stage Deterministic Vision Pipeline](#16-stage-deterministic-vision-pipeline)
- [Aerial Disaster & Tactical Demonstration Gallery](#aerial-disaster--tactical-demonstration-gallery)
  - [1. Urban Earthquake Ruin Segmentation (Hatay / Antakya)](#1-urban-earthquake-ruin-segmentation-hatay--antakya)
  - [2. Low-Altitude Rubble & Debris Delineation (Elbistan)](#2-low-altitude-rubble--debris-delineation-elbistan)
  - [3. Emergency Rescue Vehicle Access Corridor Delineation](#3-emergency-rescue-vehicle-access-corridor-delineation)
  - [4. Macro-Orthomosaic Aerial Pathfinding & UGV (İKA) Routing](#4-macro-orthomosaic-aerial-pathfinding--ugv-ika-routing)
  - [5. Tactical Helipad & Aerial Landing Zone Identification (H-MOD)](#5-tactical-helipad--aerial-landing-zone-identification-h-mod)
- [Technical Specifications & Embedded Benchmarks](#technical-specifications--embedded-benchmarks)
- [License](#license)

---

## Why This Project

In the immediate aftermath of catastrophic seismic disasters (such as the February 6, 2023 Kahramanmaraş and Hatay earthquakes), ground navigation for search-and-rescue teams, ambulances, and unmanned ground rescue rovers (**İKA / UGV**) is paralyzed. Collapsed multi-story buildings, dense rubble mounds, and surface fractures block arterial roads.

Traditional satellite observation is hindered by orbital revisit latency, atmospheric occlusion, and low ground sampling distance. **UAV-Pathfinder** provides immediate, low-altitude aerial reconnaissance. By executing a lightweight, deterministic 16-stage computer vision filtering architecture directly on low-power embedded hardware (e.g., Raspberry Pi 5), the system segments navigable roadways from structural debris in real time, extracts topological centerlines, and computes collision-free passage corridors without requiring cloud infrastructure or heavyweight deep-learning servers.

> **Note:** This repository is an **open visual demonstration showcase** compiling curated high-resolution screenshots, feature filter stage breakdowns, and real-time aerial processing animations from real-world disaster surveys and tactical simulations.

---

## System Overview

```mermaid
flowchart TD
    A["Raw Aerial UAV Monocular Feed<br/>(640x360 @ 30 FPS)"] --> B["Multi-Band Color & Contrast Conditioning<br/>(CLAHE + 6-Band HSV Road Profiling)"]
    B --> C["Structural Feature Extraction<br/>(Multi-Orientation Gabor Bank + Directional Coherence)"]
    B --> D["Topological & Roughness Discrimination<br/>(Morphological Top-Hat + High-Entropy Debris Filter)"]
    C --> E["Probabilistic Spatial Confidence Map<br/>(Confidence Score Fusion & Dual Hysteresis)"]
    D --> E
    E --> F["Navigable Road Mask (Yol)<br/>Green Boundary Overlay"]
    E --> G["Impassable Debris Mask (Enkaz)<br/>Red Exclusion Polygon"]
    F --> H["Topological Centerlines & Hough Vectors<br/>Directional Waypoint Graph"]
    H --> I["Autonomous UGV (İKA) Rescue Routing<br/>& Precision Helipad (H-MOD) Extraction"]
```

---

## 16-Stage Deterministic Vision Pipeline

Rather than relying on black-box neural networks that struggle with out-of-distribution disaster rubble, **UAV-Pathfinder** uses a deterministic 16-stage computer vision pipeline designed for mathematical interpretability, high edge throughput, and robustness against dust, shadows, and fragmented asphalt:

| 16-Stage Computer Vision Feature Extraction Pipeline |
|:---:|
| ![16-Stage Feature Extraction Pipeline](assets/images/cv_pipeline_01_16_stage_feature_breakdown.jpg) |
| *Visual breakdown of the 16 parallel and sequential filter outputs executed on a single disaster aerial frame.* |

### Pipeline Stage Breakdown:

1. **Original Grayscale Conversion:** Normalizes raw dynamic range across varied sensor exposure.
2. **CLAHE Enhancement:** Contrast Limited Adaptive Histogram Equalization recovers road boundary detail under harsh glare and shadows.
3. **Texture Gradient Estimation:** Quantifies high-entropy micro-textures characteristic of broken concrete and shattered masonry.
4. **Morphological Top-Hat Filter:** Extracts locally uniform, flat road surfaces from surrounding structural rubble.
5. **Canny Edge Density Mapping:** Identifies chaotic high-frequency structural collapse vs. continuous road asphalt.
6. **Flatness Gradient:** Differentiates smooth drivable pavement from high-roughness obstacle surfaces.
7. **6-Band HSV Road Spectral Filtering:** Adaptively segments asphalt and compacted dirt roads across 6 color sub-bands.
8. **Vegetation & Sky Masking:** Excludes non-traversable foliage, tree canopies, and horizon artifacts.
9. **Directional Edge Coherence:** Highlights continuous, aligned road shoulders and curb lines.
10. **Gabor Linearity Bank:** Multi-scale spatial frequency filtering identifying linear road patterns.
11. **Gabor Orientation Map:** Polar color wheel projection encoding local surface orientation vectors.
12. **Fused Probabilistic Confidence Map:** Weighted linear combination of texture, color, and geometric confidence metrics.
13. **Dual-Threshold Hysteresis Seeds:** Propagates connectivity seeds outward from high-certainty road centers.
14. **Final Road Mask (`Yol`):** Morphologically closed polygon representing safe navigable road space.
15. **Debris Exclusion Mask (`Enkaz`):** Segmented structural collapse areas flagged with keep-out boundaries.
16. **Topological Centerline & Hough Vectors:** Skeletonized travel axes providing waypoint graphs for autonomous ground vehicles.

---

## Aerial Disaster & Tactical Demonstration Gallery

### 1. Urban Earthquake Ruin Segmentation (Hatay / Antakya)

Aerial survey over heavily destroyed city blocks in Antakya (07.02.2023). The system isolates passable arterial corridors (`ROAD #2 17230px`) from massive collapsed residential zones (`DEBRIS 89110px`), updating a persistent spatial SmartMap.

| Aerial Survey Animation | Segmented Road & Debris Analysis |
|:---:|:---:|
| ![Hatay Road Debris Demo](assets/images/demo_01_hatay_aerial_road_debris_segmentation.gif) | ![Hatay Road Debris Still](assets/images/aerial_seg_01_hatay_earthquake_road_debris.jpg) |
| **Real-Time Aerial Segmentation:** Processing low-altitude footage over earthquake ruins; separates arterial roadways (green) from collapsed buildings (red) with real-time Canny PIP inset. | **Topological SmartMap Output:** Frame telemetry showing 12 active spatial zones, road centerline derivation, and debris boundary containment. |

---

### 2. Low-Altitude Rubble & Debris Delineation (Elbistan)

Low-altitude UAV flight over structural wreckage in Elbistan. Demonstrates fine-grained multi-segment road tracking (`ROAD #1`, `#2`, `#3`) through narrow gaps between collapsed structures.

| Ruin Navigation Animation | Multi-Segment Corridor Extraction |
|:---:|:---:|
| ![Elbistan Ruins Demo](assets/images/demo_02_elbistan_ruins_low_altitude_survey.gif) | ![Elbistan Ruins Still](assets/images/aerial_seg_02_elbistan_low_altitude_rubble_survey.jpg) |
| **Narrow Corridor Detection:** Real-time adaptation to partially blocked roadways and rubble spills at low altitudes. | **Debris Margin Classification:** Accurate identification of 36,545 px and 21,500 px rubble obstructions encroaching into travel paths. |

---

### 3. Emergency Rescue Vehicle Access Corridor Delineation

Aerial guidance for emergency ground responders. The pipeline identifies clear corridors directly through disaster-stricken streets, ensuring ambulances and heavy rescue machinery avoid debris bottlenecks.

| Access Corridor Animation | Ground Team Waypoint Projection |
|:---:|:---:|
| ![Rescue Access Demo](assets/images/demo_03_rescue_vehicle_access_pathfinding.gif) | ![Rescue Access Still](assets/images/aerial_seg_03_search_rescue_vehicle_corridor.jpg) |
| **Real-Time Search & Rescue Guidance:** Tracking navigable road ribbons directly past active emergency vehicle convoys and personnel. | **Corridor Boundary Verification:** Green drivable boundaries alongside red debris zones with yellow travel centerlines. |

---

### 4. Macro-Orthomosaic Aerial Pathfinding & UGV (İKA) Routing

End-to-end topological path planning across an entire reconstructed disaster zone. The system processes an aerial orthomosaic map, identifies all rubble heaps (`Enkaz` with red X markers), and computes the optimal collision-free route (`İKA rotası`) for an autonomous ground rescue rover to reach the designated target.

| Autonomous UGV / İKA Navigation Animation | Complete Macro-Terrain Orthomosaic Map |
|:---:|:---:|
| ![UGV Path Planning Demo](assets/images/demo_04_autonomous_ugv_ika_path_planning.gif) | ![Macro Orthomosaic Map Still](assets/images/macro_map_01_pathfinder_v7_orthomosaic_planning.jpg) |
| **Autonomous Rover Dispatch:** Automated traversal animation showing the rover (`İKA`) executing the calculated trajectory past collapsed structures. | **Topological Grid Representation:** Pathfinder v7.1 global overview showing complete segmented road topology, debris zones, and target landing point. |

---

### 5. Tactical Helipad & Aerial Landing Zone Identification (H-MOD)

Tactical aerial landing zone (`H-MOD`) detection for emergency casualty evacuation and relief airlifts. The system automatically identifies flat, debris-free landing pads (`H Hedef`) and verifies safety clearances.

| Helipad Tracking Animation | Landing Zone Clearance Extraction |
|:---:|:---:|
| ![Helipad Detection Demo](assets/images/demo_05_tactical_helipad_emergency_landing.gif) | ![Helipad Still](assets/images/helipad_01_emergency_landing_zone_detection.jpg) |
| **Dynamic Helipad Acquisition:** Rapidly detects and locks onto tactical landing markings while identifying surrounding ground hazards. | **Clearance & Target Fix:** Verification of surrounding obstacle-free radii with real-time waypoint alignment. |

---

## Technical Specifications & Embedded Benchmarks

| Metric | Measured Specification |
| :--- | :--- |
| **Target Hardware** | Single-Board Computer (Raspberry Pi 5 / Cortex-A76 @ 2.4 GHz) |
| **Processing Modality** | Monocular Aerial UAV Video Stream (RGB) |
| **Input Resolution** | $640 \times 360$ pixels ($230,400\text{ px}$) |
| **Inference Latency** | **$0.31\,\text{s}$ per frame** (Standard Mode) / **$12.1\text{ FPS}$** (TURBO / Ultralight Mode) |
| **Feature Extraction** | Gabor Filter Bank ($N=4$ orientations, $k=31$, $\lambda=20\text{px}$), $21\times 21$ Coherence, Top-Hat |
| **Spatial Memory (SmartMap)** | Multi-frame temporal zone integration ($5\text{ to }77\text{ frames}$ recursive accumulation) |
| **Autonomous Routing** | Topological graph centerline extraction + collision-free UGV / İKA trajectory generation |
| **Safety Certification** | Zero-heuristic hard-margin obstacle exclusion (`Enkaz` buffer zone) |

---

## License

Distributed under the **MIT License**. All demonstration assets and documentation are openly available for research and educational purposes in post-disaster robotics and computer vision.
