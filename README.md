# Image Duplicate Finder

A robust computer vision solution for detecting duplicate and near-duplicate images using SIFT (Scale-Invariant Feature Transform) feature detection and RANSAC-based geometric verification.

## Overview

This project implements an advanced image matching system that can identify duplicate images even when they have been modified through transformations such as rotation, cropping, scaling, or horizontal flipping. The system uses classical computer vision techniques to achieve high accuracy in matching query images to their source counterparts within large image databases.

## Features

- **Transformation-Invariant Matching**: Detects duplicates despite:
  - Rotation at any angle
  - Partial cropping or occlusion
  - Scale variations
  - Horizontal flipping
  - Lighting and contrast changes

- **SIFT-Based Feature Detection**: Utilizes Scale-Invariant Feature Transform for robust keypoint detection and descriptor computation

- **Geometric Verification**: Employs RANSAC (Random Sample Consensus) algorithm to filter false matches and ensure geometric consistency

- **Efficient Processing**: Pre-computes and caches source image features to accelerate matching

- **Validation Framework**: Includes groundtruth comparison and performance metrics

## Methodology

### 1. Feature Detection (SIFT)
The system uses SIFT algorithm which:
- Detects keypoints at multiple scales using Difference-of-Gaussians (DoG)
- Computes 128-dimensional descriptors for each keypoint
- Provides invariance to rotation and scale
- Maintains robustness against illumination changes

### 2. Feature Matching
- Uses Brute-Force (BF) or FLANN-based matcher
- Applies Lowe's Ratio Test (default ratio: 0.75) to filter ambiguous matches
- Tests multiple image variants (original + horizontally flipped)

### 3. Geometric Verification (RANSAC)
- Estimates homography transformation between matched keypoints
- Filters outliers using configurable pixel tolerance
- Counts inliers as match quality score
- Ensures geometric consistency of matches

## Installation

### Prerequisites
- Python 3.11 or higher
- Virtual environment tool (recommended: `uv`)

### Setup

```bash
# Create virtual environment
uv venv image_duplicate_finder --python 3.11

# Activate virtual environment
# On Windows:
.\image_duplicate_finder\Scripts\activate

# On macOS/Linux:
source image_duplicate_finder/bin/activate

# Install dependencies
uv pip install ipykernel opencv-python pandas matplotlib tqdm

# Install Jupyter kernel
# On Windows:
image_duplicate_finder\Scripts\python -m ipykernel install --user --name=image_duplicate_finder --display-name "image_duplicate_finder"

# On macOS/Linux:
image_duplicate_finder/bin/python -m ipykernel install --user --name=image_duplicate_finder --display-name "image_duplicate_finder"
```

## Usage


### Running the Notebook

1. **Calculate Source Features** (first time only):
```python
source_features = calculate_source_features(
    feature_detector='SIFT',
    matcher='BF'
)
```

2. **Load Pre-computed Features** (subsequent runs):
```python
source_features = load_source_features(
    feature_detector='SIFT',
    matcher='BF'
)
```

3. **Find Matches**:
```python
results, non_matched = find_matches(
    query_img_files=query_img_files,
    query_img_folder=query_img_folder,
    source_features=source_features,
    ransac_threshold=5,
    acceptance_threshold=6,
    pixel_tolerance=5.0,
    lowe_ratio=0.75
)
```

### Parameters

- **`ransac_threshold`**: Minimum number of good matches before RANSAC (default: 5)
- **`acceptance_threshold`**: Minimum inliers to accept a match (default: 6)
- **`pixel_tolerance`**: RANSAC reprojection threshold in pixels (default: 5.0)
- **`lowe_ratio`**: Ratio test threshold for filtering matches (default: 0.75)

## Performance

On the validation dataset:
- **Accuracy**: 96% (48/50 correct matches)
- **Processing Time**: ~2.3 seconds per query image (on 2500 source images)
- **False Negatives**: 2 images not matched

## Output

The system generates:
- CSV file with query-source pairs
- Pickle files with detailed match results
- Visual comparison figures showing matched pairs

## Technical Details

### Algorithms
- **Feature Detector**: SIFT or ORB
- **Matcher**: Brute-Force or FLANN-based
- **Geometric Filter**: RANSAC with homography estimation

### Key Functions
- `calculate_source_features()`: Pre-computes features for all source images
- `load_source_features()`: Loads cached feature data
- `find_matches()`: Performs matching with configurable parameters
- `save_query_source_pairs_figure()`: Visualizes matched pairs

## Dependencies

- `opencv-python`: Computer vision operations
- `pandas`: Data manipulation
- `matplotlib`: Visualization
- `tqdm`: Progress bars
- `numpy`: Numerical operations

## Limitations

- SIFT is not inherently mirror-invariant (handled by testing flipped variants)
- Processing time scales linearly with database size
- Requires distinct features in images (may struggle with solid colors)

## Future Improvements


- Add support for deep learning-based features
- Optimize for GPU acceleration
- Add support for video frame deduplication

## Acknowledgments

Based on David Lowe's SIFT algorithm:
- Lowe, D. G. (2004). "Distinctive Image Features from Scale-Invariant Keypoints". International Journal of Computer Vision.

