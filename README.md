# Tic-Tac-Toe X/O Detection with OpenCV

Digital Image Processing project. The program finds the **Xs** and **Os** on a hand-drawn tic-tac-toe board (marker on a whiteboard) in every image of the dataset, circles them (**Xs in red, Os in green**), reports their cell positions, and evaluates the result against the ground-truth labels using RMSE.

## Task

Using OpenCV, find the Xs and Os in all 64 images of the dataset. For each image:

1. Output the image with Xs circled in red and Os circled in green.
2. Print the positions (cells are numbered 1-9, left to right, top to bottom):

```
Xs Positions -> [3, 4, 5]
Os Positions -> [1, 2]
```

3. Compare with the labels and compute the RMSE for X and O.

Cell numbering:

```
1 | 2 | 3
---------
4 | 5 | 6
---------
7 | 8 | 9
```

## Example

| Input | Output |
|---|---|
| Hand-drawn board | Xs circled in red, Os circled in green |

Add your own screenshots to a `docs/` folder and link them here.

## Repository structure

```
.
├── code.ipynb   # Notebook (code cells + markdown description for each)
├── dataset_images_XO.zip/
│   ├── images/                 # 64 input images
|
│── GT.json                     # data labels
├── output.zip/                 # annotated images + results_table.csv
└── README.md
```

## Image processing pipeline

| Step | Operation | OpenCV |
|---|---|---|
| 1 | Load and resize images | `imread`, `resize` |
| 2 | Grayscale | `cvtColor` |
| 3 | Illumination correction (closing to estimate the background, then divide) | `morphologyEx`, `divide` |
| 4 | Noise reduction | `GaussianBlur` |
| 5 | Otsu threshold (ink = white) | `threshold` |
| 6 | Morphological opening and closing | `morphologyEx` |
| 7 | Grid detection with long horizontal and vertical kernels | `morphologyEx`, `findContours` |
| 8 | Grid line removal | `bitwise_and` |
| 9 | Split the board into 9 cells and extract the symbol in each | `connectedComponentsWithStats` |
| 10 | Classify X vs O | center-density feature |
| 11 | Draw red circles (X) and green circles (O) | `minEnclosingCircle`, `circle` |
| 12 | Print positions | |
| 13-14 | Load labels, compute RMSE, build results table | `pandas`, `numpy` |

### How X and O are told apart

For each symbol, the ink density is measured in the central window (middle 40% of its bounding box). An **X** has two strokes crossing in the center (high density). An **O** is a ring with an empty center (density near 0). The threshold is `CENTER_THR` (default 0.15).

## Evaluation

Two RMSE definitions are reported, for X and for O:

- **Position RMSE:** each image becomes a 9-element 0/1 vector (1 = symbol in that cell), and the RMSE is taken over all images and cells.
- **Count RMSE:** the RMSE between the true and predicted number of symbols per image.

Results table format:

| Image Name | Xs (True) | Xs (Output) | Os (True) | Os (Output) |
|---|---|---|---|---|
| image_01.jpeg | [3, 4, 5] | [3, 4, 5] | [1, 2] | [1, 2] |
| ... | ... | ... | ... | ... |
| **RMSE** | | ?? | | ?? |

Fill in your final numbers after running the notebook.

## How to run

### Google Colab (recommended)

1. Open `tictactoe_detection.ipynb` in Colab.
2. Upload the dataset (or mount Google Drive).
3. Edit the **configuration cell**:

```python
IMAGES_DIR  = "/content/dataset/images"
LABELS_FILE = "/content/dataset/labels.csv"
OUTPUT_DIR  = "/content/output"
LABEL_IMG_COL = "image_name"
LABEL_X_COL   = "X_positions"
LABEL_O_COL   = "O_positions"
```

4. Run the cells one by one (the code uses no user-defined functions).

### Local

```bash
pip install opencv-python numpy pandas matplotlib
python tictactoe_detection.py
```

## Tunable parameters

| Parameter | Default | Purpose |
|---|---|---|
| `RESIZE_W` | 800 | Width all images are resized to |
| `MIN_AREA` | 30 | Smallest connected component kept as ink |
| `MIN_INK` | 150 | Minimum ink pixels for a cell to contain a symbol |
| `MIN_SIZE_FRAC` | 0.30 | A symbol must span this fraction of the cell |
| `CENTER_THR` | 0.15 | X vs O decision threshold |

## Requirements

- Python 3.8+
- opencv-python, numpy, pandas, matplotlib
- Google Colab

## Notes and limitations

- Assumes the board is roughly upright and the grid lines are long and continuous.
- If the grid lines are not detected, the code falls back to dividing the ink bounding box into thirds and prints a message.
- Very faint or broken marker strokes may need lower `MIN_INK`/`MIN_AREA` values.

## License

Educational project. Add a license of your choice (for example MIT).
