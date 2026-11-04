# Tic-Tac-Toe-Images-processing
# Deterministic Spatial Parsing and Classification via Classical Computer Vision

This repository contains a zero-latency, classical computer vision pipeline built entirely in OpenCV and Python. It is designed to ingest hand-drawn whiteboard images of Tic-Tac-Toe boards, structurally isolate the grid, filter geometric anomalies, classify the symbols ('X' vs 'O'), and map them to a discrete 2D spatial grid.

## Project Motivation
While deep learning object detection models (e.g., YOLO) are standard for spatial parsing, they carry high computational overhead. This project demonstrates foundational matrix-level engineering and morphological mathematics, providing a deterministic, ultra-fast alternative suitable for offline, resource-constrained edge AI environments.

## Methodology & Engineering Architecture

1. **Illumination Invariance:** Applied morphological background subtraction and Otsu's thresholding to flatten severe ambient lighting gradients across whiteboard media.
2. **Morphological Severing:** Engineered a dynamic "cookie-cutter" dilation mask to forcefully sever topological fusions where hand-drawn symbols intersected with structural grid lines.
3. **Geometric Anomaly Rejection:** Replaced brittle pixel-subtraction methods with dynamic multi-constraint filters (Aspect-Ratio and Convex-Hull Solidity) to accurately reject structural fragments (e.g., L-shaped grid corners) while preserving true positive symbols.
4. **Spatial Coordinate Normalization:** Utilized geometric image moments (`cv2.moments`) to map localized physical contours to a discrete $3 \times 3$ mathematical array, bypassing bounding-box variance.

## Evaluation Protocol
The pipeline's spatial accuracy is benchmarked against a 64-image ground truth dataset (`GT.json`). Predictions are converted into 9-dimensional binary vectors, and geometric detection accuracy is measured via **Root Mean Squared Error (RMSE)**. This strictly penalizes both positional False Positives and False Negatives, providing a more rigorous metric than raw symbol counting.

## Environment Setup
Reproduce this project using Anaconda:

```bash
conda env create -f environment.yml
conda activate cv-pipeline-env
jupyter notebook
