# Preprocessing: Creating a Balanced Frame Dataset

This preprocessing step transforms raw squat videos into a balanced image dataset across the three phases: `pre`, `per`, and `post`.

## Objective

Videos have different durations depending on the group, resulting in significant differences in the number of available frames.

Training a model on this raw data could introduce a bias toward the groups with the largest number of frames.

To prevent this imbalance, we extract the same number of frames from each group.

## Input Data

Videos are stored in `data/raw/` and follow the naming pattern:

- `squat_pre*.mp4`
- `squat_per*.mp4`
- `squat_post*.mp4`

## Processing Steps

### 1. Count Frames per Group

For each video, OpenCV retrieves the number of frames and the FPS (metadata). The frame counts are then summed for each group.

A summary table is displayed containing the following information:

- Group
- Filename
- Number of frames
- FPS
- Duration

![Frames per group before balancing](figures/frames_per_group_before_squat.png)

### 2. Define the Target Number of Frames

We use **10% of the number of frames in the smallest group**.

This value becomes the common target number of frames for all three groups.

### 3. Extract Uniformly Spaced Frames

For each group:

1. Frame indices are uniformly distributed across all videos in the group (step = total number of frames / target number of frames).
2. Global frame indices are converted into local indices for each video.
3. Videos are read sequentially, and the selected frames are saved as `.jpg` images.

This uniform sampling strategy ensures coverage across the entire duration of the videos.

### 4. Verify Dataset Balance

The number of images in each output directory is counted, and a final bar chart is generated.

The three groups should contain approximately the same number of images.

![Frames per group after balancing](figures/frames_per_group_after_squat.png)

## Output Structure

```text
data/processed/
├── dataset_squat_pre/    # <video_name>_frame_<index>.jpg
├── dataset_squat_per/
└── dataset_squat_post/
```

## Installation and Usage

Install the required Python dependencies:

```bash
pip install opencv-python numpy pandas matplotlib
```

Launch the preprocessing notebook:

```bash
jupyter notebook preprocessing/extraction_frames.ipynb
```

## Notes

- Raw videos and extracted images are not version-controlled (see `.gitignore`).
- The number of frames reported by video metadata may slightly differ from the actual number of decodable frames, depending on the video codec.