# SI Figure S3: Droplet Segmentation Benchmark

## 1. Source Image
*   **Filename**: `S3a_raw_green.png` (Derived from `drop.tif`)
*   **Description**: Raw low-light green fluorescence microscopy image of microfluidic droplets. Normalized from 16-bit to 8-bit [0-255].

## 2. Methodology & Parameters

### (b) Otsu's Thresholding
*   **Preprocessing**: Gaussian Blur (kernel 5x5).
*   **Thresholding**: Otsu's binarization (global).
*   **Post-processing**: Contour extraction via `cv2.findContours`.

### (c) Watershed Segmentation
*   **Preprocessing**: Otsu binarization + Morphological Opening (kernel 3x3, iter=2).
*   **Markers**:
    *   Sure Background: Dilation (iter=3).
    *   Sure Foreground: Distance Transform (threshold > 0.5 * max).
    *   Unknown Region: Subtraction of Background - Foreground.

### (d) Hough Circle Transform
*   **Preprocessing**: CLAHE (ClipLimit=2.0, Tile=8x8) + Gaussian Blur (kernel 3x3).
*   **Parameters**:
    *   `dp` (Resolution inverse ratio): 1
    *   `minDist`: 20 pixels
    *   `param1` (Canny High Threshold): 50
    *   `param2` (Accumulator Threshold): 12
    *   `minRadius`: 12 pixels
    *   `maxRadius`: 50 pixels

### (e) Mask R-CNN (Deep Learning)
*   **Model**: ResNet-50-FPN pre-trained on COCO (torchvision default weights).
*   **Inference Strategy**: Zero-shot transfer (No fine-tuning).
    *   **Preprocessing**: CLAHE enhancement.
    *   **Tiling**: 640x640 patch size, 25% overlap.
    *   **Threshold**: Score > 0.05.
    *   **NMS**: IoU threshold 0.3.

## 3. Environment
*   **Python**: 3.12.12
*   **OpenCV**: 4.12.0
*   **PyTorch**: 2.9.1
*   **Torchvision**: 0.24.1
