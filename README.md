# Facial Feature Classification Challenge

ENGE707 deep-learning project for classifying cat and dog facial features into eight classes: `CatEye`, `DogEye`, `CatFace`, `DogFace`, `CatMouth`, `DogMouth`, `CatNose`, and `DogNose`. Kaggle evaluates classification accuracy.

The project includes exploration, preprocessing, a custom CNN baseline, ResNet18 transfer learning, evaluation, submission generation and controlled improvement experiments. The ResNet50 experiment and unused Notebook 13 are no longer part of the active project.

## Recorded results

| Model or experiment | Validation accuracy | Context |
|---|---:|---|
| Custom CNN | 97.83% | Original split; 13 completed epochs |
| ImageNet-pretrained ResNet18 | 99.65% | Original split; 10 completed epochs |
| Original ResNet18 with horizontal-flip TTA | 99.68% | Original split; 41 errors instead of 44 |
| Perceptually grouped ResNet18 | 99.62% | Different validation split; not directly comparable |

The [saved public leaderboard screenshot](outputs/figures/kaggle_leaderboard_score.png) records **94.63%** for the original ResNet18 submission. This does not establish the private leaderboard score or the score of the later full-data model. See the [original model comparison](outputs/evaluation/model_comparison.csv) and [group-aware training summary](outputs/models/group_aware_baseline/group_aware_resnet18_training_summary.json).

The validation–Kaggle gap remains a limitation. The split audit identified 398 same-class near-duplicate candidate pairs crossing the original split. Later perceptual grouping separates detected same-class near-duplicates, but does not guarantee separation of every related crop or animal.

## Project structure

```text
notebooks/                 Exploration, training, evaluation and experiments
data/                      Original competition images and CSVs (not tracked)
splits/                    Original training and validation manifests
  group_aware/             Separate perceptually grouped manifests
models/                    Local trained checkpoints (not tracked)
outputs/
  quality_checks/          Dataset counts, image properties and duplicate checks
  preprocessing/           Label mapping, preprocessing configuration and checks
  models/                  Model settings, training histories and summaries
  evaluation/              Validation metrics, predictions and model selection
  submission/              Original submission and prediction evidence
  split_audit/             Perceptual grouping and split audit
  improvements/            TTA, geometry and historical diagnostic evidence
  figures/                 Report figures and leaderboard screenshot
README.md
requirements.txt
```

Dataset files, checkpoints, ZIP archives, `.venv/` and `tmp/` are excluded from Git. These must be supplied separately after cloning. Historical mixed-model diagnostic outputs are retained as evidence only and are not dependencies of the active notebooks.

## Data and environment setup

Obtain the supplied dataset through the competition, following its access and sharing rules. Expected local layout:

```text
data/
  train/train/*.jpg
  test/test/*.jpg
  train_labels.csv
  sample_submission.csv
```

There are 63,660 labelled images and 29,443 unlabelled test images. Original files remain unchanged: CSV manifests define subsets and transformations happen in memory. Classes are moderately imbalanced.

From the project directory on macOS/Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python -m ipykernel install --user --name enge707-kaggle --display-name "Python (ENGE707 Kaggle)"
jupyter lab
```

Select the project environment as the notebook kernel. Requirements are not version-pinned; executed notebooks record versions from the saved runs. Runtime and exact GPU results can vary between environments.

## Original workflow: Notebooks 1–6

| Notebook | Purpose |
|---|---|
| [01_dataset_exploration](notebooks/01_dataset_exploration.ipynb) | Check file structure, class balance, representative training images, dimensions, readability and duplicates; save quality evidence. |
| [02_preprocessing_and_split](notebooks/02_preprocessing_and_split.ipynb) | Use exploration outputs to group exact duplicates and save the original 80/20 split, label mapping and preprocessing configuration. |
| [03_custom_cnn_pipeline](notebooks/03_custom_cnn_pipeline.ipynb) | Develop and train the custom CNN; save `models/custom_cnn_best.pth` and baseline evidence. Contains both pipeline development and training. |
| [04_resnet18_transfer_learning](notebooks/04_resnet18_transfer_learning.ipynb) | Train ResNet18 using the same original split and ImageNet weights; save `models/resnet18_best.pth` and training evidence. |
| [05_model_evaluation](notebooks/05_model_evaluation.ipynb) | Load both checkpoints and evaluate the original validation split; save metrics, confusion matrices, errors and `outputs/evaluation/selected_model.json`. No training. |
| [06_kaggle_submission](notebooks/06_kaggle_submission.ipynb) | Load the original selected ResNet18 checkpoint and selection metadata, generate test predictions and save the verified `outputs/submission/submission.csv`. No training. |

**Check switches before Run All.** Saved training notebooks currently contain `RUN_TRAINING = True`; running them may retrain models and overwrite designated outputs. Set it to `False` when inspecting existing runs, retaining required checkpoints and histories. Notebook 5 has a separate `RUN_EVALUATION` switch. Submission inference does not require repeating training.

## Running on Kaggle

For training, enable a supported GPU and attach the competition dataset, a private artifact dataset with the appropriate split CSVs and `preprocessing_config.json`, and the official `resnet18-f37072fd.pth` ImageNet weights if Internet is disabled.

For Notebook 5, also attach the original custom CNN and ResNet18 checkpoints. For Notebook 6, attach the selected ResNet18 checkpoint and completed Notebook 5 output containing `selected_model.json`. Avoid conflicting versions of identically named inputs and check printed paths.

Inputs under `/kaggle/input` are read-only; generated files go under `/kaggle/working`. Download both the executed notebook and its result package. Keep checkpoints associated with their exact split and configuration. GPU times vary; a configured time guard is not an exact wall-clock guarantee.

## Optional experiments: Notebooks 7–11

These extend the original workflow; they are not prerequisites for its submission.

| Notebook | Purpose |
|---|---|
| [07_controlled_tta_validation_experiment](notebooks/07_controlled_tta_validation_experiment.ipynb) | Compare original-checkpoint baseline inference and horizontal-flip TTA on the original validation split; no retraining. |
| [08_group_aware_validation_split](notebooks/08_group_aware_validation_split.ipynb) | Audit perceptual similarity and save separate manifests in `splits/group_aware/`; preserve the original split. |
| [09_group_aware_resnet18_baseline](notebooks/09_group_aware_resnet18_baseline.ipynb) | Retrain on Notebook 8's split; save `models/group_aware_baseline/resnet18_group_aware_best.pth` separately. |
| [10_geometry_robustness_audit](notebooks/10_geometry_robustness_audit.ipynb) | Evaluate deterministic image views and prediction combinations using the group-aware checkpoint. This tests inference geometry, not separate models retrained for each geometry. |
| [11_final_resnet18_full_data](notebooks/11_final_resnet18_full_data.ipynb) | Train on all 63,660 labelled images after validation-led development and generate a separate submission. This final fit has no held-out validation set. |

Notebook 11 uses separate full-data artifacts and `submission_resnet18_full_data.csv`. Its training accuracy is not validation accuracy, and its predictions are not the original 94.63% submission. If a requested final checkpoint is missing locally, restore it from the completed run package rather than silently substituting another checkpoint.

## Evidence for the report

- [Training configuration comparison](outputs/models/training_configuration_comparison.md): original custom CNN and ResNet18 settings, with supporting links.
- `outputs/models/`: training histories, hyperparameters, stage definitions, stopping reasons and summaries.
- `outputs/evaluation/`: original validation accuracy, loss, precision, recall, F1, confusion pairs and model-selection metadata.
- `outputs/improvements/tta_validation/`: paired TTA evaluation and decision evidence.
- `outputs/split_audit/` and `splits/group_aware/`: grouping audit and separate manifests.
- `outputs/improvements/geometry_robustness/`: geometry comparisons.
- `outputs/figures/`: report figures and Kaggle evidence.

## Reproducibility and evaluation safeguards

The original pipeline uses RGB conversion, black square padding, bilinear resizing to 224 × 224, tensor conversion and ImageNet normalisation. Random flips, rotation and colour jitter are training-only; original validation and submission transforms are deterministic. Class indices are reused and predictions preserve `sample_submission.csv` order and official label spelling.

Use seed 42 and saved manifests to reproduce experiments. Compare models on the same holdout. Do not evaluate an old checkpoint on a new split that may contain its training images. Preserve baseline checkpoints before new experiments. Hidden test images must not be manually labelled or used for model/hyperparameter selection; prediction counts alone cannot identify test-class errors.

The report is a separate deliverable. Submit it and required evidence according to the course instructions, with appropriate citations. Do not publish private competition images or trained artifacts unless permitted.
