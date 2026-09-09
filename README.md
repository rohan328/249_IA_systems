# Does Relative Point Support Improve Trust under Sparse-LiDAR Shift?

## Team Members
- Rohan Ohlan

## Selected Track
Safety and Evaluation

## Abstract
LiDAR detectors can produce confident but incorrect 3D boxes when objects receive too few supporting points because of distance, occlusion, reduced sensor resolution, or point corruption. This project investigates whether a lightweight post-hoc trust monitor can detect such unreliable predictions without retraining the detector.

A frozen PointPillars model is used to generate Car detections on clean and artificially sparsified KITTI point clouds. The proposed monitor augments standard detector features with relative point support: the difference between the observed number of points in a predicted box and the number expected for a clean, correct detection at the same range and geometry. The study evaluates whether this clean-anchored residual improves selective risk ranking under sparse-LiDAR shift compared with native confidence, confidence plus range, and a strong post-NMS density-and-geometry baseline.

The project focuses on a controlled clean-to-sparse transfer setup, using a fixed candidate set and matched coverage-based evaluation. The key claim is not that the monitor introduces a new detector or a general safety guarantee, but that a physically interpretable point-support residual can provide measurable value for trusted prediction selection under realistic sparse-input conditions.
