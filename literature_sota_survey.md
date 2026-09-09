# Literature and SOTA Survey

## Scope

This survey covers recent work from 2023–2026 on LiDAR robustness, cross-resolution generalization, density/range modeling, uncertainty estimation, detector calibration, and post-hoc prediction-quality estimation. The project is positioned as a selective-trust study under controlled sparse-input shifts.

## Recent papers and models

| Year | Work and venue | Main result | Relationship to this project |
|---|---|---|---|
| 2023 | Kong et al., [Robo3D: Towards Robust and Reliable 3D Perception against Corruptions](https://openaccess.thecvf.com/content/ICCV2023/html/Kong_Robo3D_Towards_Robust_and_Reliable_3D_Perception_against_Corruptions_ICCV_2023_paper.html), ICCV 2023 | Introduces corruption benchmarks for 3D perception and shows substantial robustness gaps. | Establishes corruption robustness as an existing area. Our work adds object-level selective acceptance rather than another mAP-only corruption benchmark. |
| 2024 | Eskandar et al., [An Empirical Study of the Generalization Ability of LiDAR 3D Object Detectors to Unseen Domains](https://arxiv.org/abs/2402.17562), CVPR 2024 | Evaluates nine detectors across sensor-resolution, weather, and location shifts, including KITTI-32 and KITTI-16 variants. | Directly motivates the simulated cross-resolution conditions and shows that low-resolution generalization is not itself novel. |
| 2024 | Lu et al., [Range-Aware Attention Network for LiDAR-Based 3D Object Detection With Auxiliary Point Density Level Estimation](https://ieeexplore.ieee.org/document/10666000), IEEE Transactions on Intelligent Transportation Systems | Uses range-aware BEV processing and an auxiliary density-level objective to improve detection, especially for sparse or occluded objects. The journal publication is from 2024; the earlier arXiv version originated in 2021. | Demonstrates that range and point density are established detector-side signals. Our method uses them only in a frozen-detector trust monitor. |
| 2024 | Durasov et al., [Uncertainty Estimation for 3D Object Detection via Evidential Learning](https://arxiv.org/abs/2410.23910), arXiv preprint | Adds evidential uncertainty to 3D detection and evaluates unfamiliar scenes, localization quality, and missing detections. | Represents train-time/model-integrated uncertainty. Our post-hoc monitor avoids detector retraining and prediction sampling. |
| 2024 | Kuzucu et al., [On Calibration of Object Detectors: Pitfalls, Evaluation and Baselines](https://link.springer.com/chapter/10.1007/978-3-031-72664-4_11), ECCV 2024 | Identifies pitfalls in D-ECE/AP-based evaluation and evaluates temperature, Platt, and isotonic post-hoc calibration for primarily 2D object detectors. | This is a non-LiDAR methodological reference. It motivates the calibration protocol and a score-only Platt baseline. Monotonic calibration is evaluated for probability quality, not claimed as an improved selective ranking. |
| 2024 | Lee et al., [Sparse-to-Dense LiDAR Point Generation by LiDAR-Camera Fusion for 3D Object Detection](https://arxiv.org/abs/2409.14985), arXiv preprint | Uses camera-LiDAR fusion to generate denser point clouds for distant-object detection. | Represents input-recovery approaches. It changes the sensing pipeline, whereas our method flags risk without adding a sensor or changing the detector. |
| 2025 | Riedlinger et al., [LMD: Light-Weight Prediction Quality Estimation for Object Detection in LiDAR Point Clouds](https://link.springer.com/article/10.1007/s11263-025-02377-8), International Journal of Computer Vision | LidarMetaDetect uses roughly 90 features from box geometry, point counts/fractions, reflectance, and pre-NMS proposal statistics and evaluates linear and nonlinear meta-classifiers/regressors. | This is the closest published prior work. It makes a generic post-hoc confidence-plus-density monitor non-novel. Our comparator is explicitly a **post-NMS LMD-feature baseline**, not LMD or a reproduction of full LMD. |
| 2025 | Schröder et al., [Calibrating the Full Predictive Class Distribution of 3D Object Detectors for Autonomous Driving](https://ieeexplore.ieee.org/document/11097526), *IEEE Intelligent Vehicles Symposium (IV) 2025*, pp. 187–194, [DOI: 10.1109/IV64158.2025.11097526](https://doi.org/10.1109/IV64158.2025.11097526) | Evaluates post-hoc and train-time calibration for CenterPoint, PillarNet, and DSVT-Pillar, including calibration beyond the dominant class. | Supports the claim that native 3D detector scores are not automatically reliable probabilities. The first project phase is Car-only, so full multiclass calibration remains future work. |
| 2026 | Chandorkar et al., [Comprehensive Robustness Analysis of LiDAR-Based 3D Object Detection in Autonomous Driving](https://arxiv.org/abs/2607.02074), arXiv preprint | Evaluates modern and legacy detectors under adversarial point modifications using confidence, range, density, and localization factors in addition to mAP. | Reinforces the need for analysis beyond mAP, while differing from our non-adversarial selective-prediction objective. |
| 2026 | Beemelmanns et al., [Query2Uncertainty: Robust Uncertainty Quantification and Calibration for 3D Object Detection under Distribution Shift](https://arxiv.org/abs/2605.05328), CVPR 2026 | Introduces density-aware post-hoc calibration using latent object-query feature density and evaluates classification and regression uncertainty for camera- and LiDAR-based 3D detectors under distribution shift. | This is essential current SOTA. It establishes that density-aware post-hoc 3D calibration under shift is not new. Our study instead uses observable physical point support from a frozen non-query PointPillars detector and evaluates selective acceptance under controlled sparse-LiDAR shifts. |

## Foundational implementation resources

- [KITTI 3D Object Detection](https://www.cvlibs.net/datasets/kitti/eval_object.php?obj_benchmark=3d): 7,481 labeled training frames, official difficulty rules, `IoU3D = 0.70` for Car, and 40-point recall evaluation.
- [OpenPCDet](https://github.com/open-mmlab/OpenPCDet): PointPillars implementation, pretrained KITTI models, rotated NMS, 3D IoU operations, and KITTI evaluation utilities.
- [MetaDetect3D](https://github.com/JanMarcelKezmann/MetaDetect3D): public implementation associated with LidarMetaDetect.

## Baselines and what each tests

| Baseline | Features or operation | Metrics it can improve |
|---|---|---|
| Native confidence | PointPillars score | Reference ranking and uncalibrated probability quality |
| Temperature scaling | `sigmoid(logit(score) / T)` fitted on calibration frames with `T > 0` | Calibration metrics only; strict ordering, risk-coverage, and AURC remain unchanged |
| Score-only Platt scaling | `sigmoid(a * logit(score) + b)` fitted on calibration frames with `a > 0` | Parametric calibration baseline; strict ordering remains unchanged |
| Isotonic regression | Nondecreasing mapping fitted on calibration frames | Calibration metrics; it can create ties, so selective metrics use tie-averaged evaluation rather than arbitrary ordering |
| Confidence plus range | Logistic regression | Tests whether distance alone explains errors |
| Post-NMS LMD-feature baseline (LMD-lite) | Confidence, horizontal range, box geometry, point count, density, frame count | Closest feasible prior-work feature baseline; it omits full LMD's reflectance and pre-NMS proposal features and must not be called LMD |
| Proposed relative-support monitor | LMD-lite features plus expected-support residual, global support ratio, and predeclared interactions | Tests whether deviation from range-conditioned expected point support transfers better under sparse-input shift |

## Research gap and novelty boundary

Point density, range awareness, sparse-LiDAR robustness, post-hoc LiDAR quality estimation, and density-aware 3D calibration under distribution shift are already established. The project therefore makes only a scoped class-project claim:

> Evaluate whether a clean-anchored, range- and geometry-conditioned physical point-support residual improves fixed-candidate clean-to-sparse selective-risk ranking for frozen, non-query PointPillars detections over native confidence, classical calibrators, and a post-NMS LMD-feature baseline, at matched coverage and without detector retraining.

The proposed feature is

```text
relative_support = log(1 + observed_box_points)
                   - expected_log_points(range, predicted_box_geometry)
```

The expectation model is fitted only on correctly matched clean predictions from the monitor-training partition and remains frozen in the clean-to-sparse, corruption-aware, and cross-corruption regimes. Correctness labels are used only during fitting; at inference, the model uses the observed point cloud and predicted box, with no ground-truth feature.

## Expected contribution

A positive result would show lower accepted-error rate or AURC under predeclared sparse conditions against the stated baselines, not superiority over full LMD. A negative result remains meaningful if it demonstrates that raw density/range features or the relative-support residual fail to transfer beyond native confidence. The contribution is empirical and safety-oriented rather than a new detector architecture, the first post-hoc LiDAR quality estimator, or the first density-aware calibration method.

## Evaluation interpretation and reporting safeguards

Primary risk-coverage, AURC, AUROC-error, and AUPRC-error results are fixed-candidate metrics: every method ranks the same post-NMS candidate table, with labels frozen through the documented KITTI/OpenPCDet wrapper. The operational threshold-transfer analysis separately filters candidates by monitor score, preserves native-score ordering among retained boxes, and re-runs the official evaluator. ECE is binary candidate-correctness ECE with 15 fixed equal-width bins; AUPRC-error is always reported with candidate count and incorrect-label prevalence. Frame bootstraps recompute the coverage cut and risk in each resample.
