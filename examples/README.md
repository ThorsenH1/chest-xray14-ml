# Synthetic prediction example

The shared engine generates the input as a deterministic mathematical pattern. The [current three-model response](swin-convnext-ensemble.json) gives 14 calibrated scores and their validation thresholds. For effusion, the synthetic score is 0.0317 with a threshold of 0.2579. [See where the BioViL-T component responded](../WHERE-THE-MODEL-LOOKS.md)

![Synthetic input phantom.png](phantom.png)

## Original model search

| Output | Previous model score |
| --- | ---: |
| Atelectasis | 0.0778 |
| Cardiomegaly | 0.0026 |
| Effusion | 0.0689 |
| Infiltration | 0.1042 |
| Mass | 0.0215 |
| Nodule | 0.0120 |
| Pneumonia | 0.0092 |
| Pneumothorax | 0.0188 |
| Consolidation | 0.0836 |
| Edema | 0.0175 |
| Emphysema | 0.0039 |
| Fibrosis | 0.0046 |
| Pleural Thickening | 0.0135 |
| Hernia | 0.0005 |

The scores demonstrate the input and output contract for the prediction command. [Complete JSON response](prediction.json)
