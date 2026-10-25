# Training configuration comparison

**Table 3.3.1: Training configuration for the custom CNN and ResNet18.**

These settings describe the original custom CNN and ResNet18 experiments using the original training-validation split. They do not describe the later group-aware or full-data experiments.

| Setting | Custom CNN | ResNet18 |
|---|---|---|
| Starting weights | Randomly initialised | ImageNet pretrained |
| Input size | 224 × 224 RGB | 224 × 224 RGB |
| Output classes | 8 | 8 |
| Optimiser | AdamW | AdamW |
| Batch size | 32 | 128 |
| Loss function | Cross-entropy | Cross-entropy with label smoothing of 0.05 |
| Initial learning rate | 0.001 for the whole model | 0.001 for the classifier during the first 2 epochs |
| Fine-tuning learning rates | Not applicable | Earlier layers: 0.00001; final residual block: 0.00005; classifier: 0.0002 |
| Weight decay | 0.0001 | 0.0001 |
| Maximum epochs | 15 | 10: 2 classifier-only, then 8 fine-tuning |
| Learning-rate scheduler | ReduceLROnPlateau | ReduceLROnPlateau |
| Scheduler monitored metric | Validation loss | Validation loss |
| Scheduler patience | 2 epochs | 1 epoch |
| Scheduler reduction factor | Multiply learning rate by 0.5 | Multiply learning rate by 0.2 |
| Early-stopping patience | 4 epochs without validation-accuracy improvement | 3 epochs without validation-accuracy improvement |
| Random seed | 42 | 42 |
| Checkpoint selection | Highest validation accuracy | Highest validation accuracy |

## Explanation for Section 3.3

AdamW was selected because it adapts parameter updates during training and applies weight decay to discourage unnecessarily large weights. The batch sizes balance memory usage and processing speed. ResNet18 initially trains only its new classifier, then uses smaller learning rates to adjust pretrained features without changing them too aggressively. Scheduling and early stopping were configured to respond when validation performance stopped improving; their inclusion does not mean they activated during every run.

## Evidence sources

- [Custom CNN hyperparameters](custom_cnn_hyperparameters.csv)
- [ResNet18 hyperparameters](resnet18_hyperparameters.csv)
- [Custom CNN pipeline configuration](custom_cnn_pipeline_config.json)
- [ResNet18 pipeline configuration](resnet18_pipeline_config.json)
- [Custom CNN notebook](../../notebooks/03_custom_cnn_pipeline.ipynb)
- [ResNet18 notebook](../../notebooks/04_resnet18_transfer_learning.ipynb)

The original runs completed 13 custom CNN epochs and 10 ResNet18 epochs. The maximum-epoch settings above are configuration values, not a claim that every planned epoch ran.
