# Facial Feature Classification Challenge

This repository contains the code and supporting results for the ENGE707 cat and dog facial-feature classification challenge.

The model will classify each image into one of eight classes:

- `CatEye`
- `DogEye`
- `CatFace`
- `DogFace`
- `CatMouth`
- `DogMouth`
- `CatNose`
- `DogNose`

The Kaggle competition uses classification accuracy as its evaluation metric.

## Current progress

Task 1, Dataset Exploration and Understanding, is complete. The first notebook:

- checks the training and test file structure;
- calculates the class distribution;
- displays representative training images;
- checks image dimensions and colour formats;
- checks for unreadable files;
- identifies low-sharpness and extreme-brightness examples;
- identifies exact duplicate images; and
- saves figures and quality-check results under `outputs/`.

Model preprocessing, training, evaluation, and Kaggle submission generation will be added in later notebooks.

## Repository structure

```text
Facial-Feature-Classification-Challenge/
├── notebooks/
│   └── 01_dataset_exploration.ipynb
├── outputs/
│   ├── figures/
│   └── quality_checks/
├── report/
├── submissions/
├── README.md
└── requirements.txt
```

The dataset, virtual environment, temporary files, and trained model weights are excluded from Git.

## Dataset setup

Download the competition dataset from Kaggle and arrange it as follows:

```text
data/
├── train/
│   └── train/
│       ├── 00001.jpg
│       └── ...
├── test/
│   └── test/
│       ├── 00001.jpg
│       └── ...
├── train_labels.csv
└── sample_submission.csv
```

The original competition data is not included in this repository. The exploration notebook reads the original files but does not rename, move, overwrite, or delete them.

## Environment setup

Open a terminal in the project folder and create a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install the current project requirements:

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

Register the environment as a Jupyter kernel:

```bash
python -m ipykernel install --user --name enge707-kaggle --display-name "Python (ENGE707 Kaggle)"
```

Open Jupyter:

```bash
jupyter lab
```

Select `Python (ENGE707 Kaggle)` as the notebook kernel.

## Running Task 1

Open and run the following notebook from top to bottom:

```text
notebooks/01_dataset_exploration.ipynb
```

The notebook uses a fixed random seed of `42` for reproducible image sampling. Generated figures and tables are written to:

```text
outputs/figures/
outputs/quality_checks/
```

## Current Task 1 findings

- 63,660 labelled training images
- 29,443 unlabelled test images
- 63,660 rows in `train_labels.csv`
- 29,443 rows in `sample_submission.csv`
- No missing training labels or image files
- No unreadable images
- All inspected images use RGB colour
- Training images use many different dimensions and require consistent resizing later
- The eight classes are moderately imbalanced, with the eye classes containing the most images
- Exact duplicate images exist and must be considered before creating the training-validation split

## Planned notebook order

Later project stages will be added in this order:

1. `01_dataset_exploration.ipynb`
2. `02_preprocessing_and_split.ipynb`
3. `03_custom_cnn_baseline.ipynb`
4. `04_resnet18_transfer_learning.ipynb`
5. `05_model_evaluation.ipynb`
6. `06_kaggle_submission.ipynb`

Additional packages, including PyTorch, will be added to `requirements.txt` when they are first used.

## Reproducibility notes

- Keep the original Kaggle dataset unchanged.
- Run notebooks in numerical order.
- Use the saved training-validation split for every model comparison.
- Apply random image changes only to the training subset.
- Keep validation and Kaggle test data separate from model training.
- Use the exact official class names when creating the final submission.
