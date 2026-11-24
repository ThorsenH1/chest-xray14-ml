# Improving the chest X-ray model

The goal is to improve macro AUROC across all 14 findings. The current officially scored model reached **0.8025**. Pneumonia and infiltration reached **0.7060** and **0.7108**, respectively, so stronger recognition of subtle findings could improve the average. [Current benchmark and per-finding results](AUROC.md)

## Why the choice of pretrained model matters

Transfer learning starts a model with visual features learned elsewhere. The experiment still needs a trustworthy answer to whether the model recognizes new images. A test image can indirectly enter training through a copy attached to a different patient record.

The CheXWorld paper reports **0.8358 macro AUROC** on its NIH test configuration. I compared its released NIH lists with the prepared project cohort before using the checkpoint.

| Provenance check | Count |
| --- | ---: |
| Project official test files listed in the authors' official test | 25,596 |
| Direct test filenames found in the authors' training list | 0 |
| Official test radiographs in duplicate-linked groups that also touch the authors' training list | 72 |
| Project validation radiographs in the authors' training list | 9,392 |

The patient-linked duplicate groups were detected by the project's exact-pixel audit. The 72 count covers every radiograph in the affected groups, including images that are themselves unique. The checkpoint is excluded from the clean full-cohort comparison. [CheXWorld paper and weights](https://github.com/LeapLabTHU/CheXWorld) · [Authors' training and evaluation split](https://openaccess.thecvf.com/content/CVPR2025/supplemental/Yue_CheXWorld_Exploring_Image_CVPR_2025_supplemental.pdf)

## A model trained on lung images from a different source

The BioViL-T image backbone learned from chest X-rays in MIMIC-CXR. This project transfers its single-image ResNet-50 and fits two scoring heads on NIH training images. One summarizes the whole image. The other scores local evidence for each finding and highlights strong regions in the final ranking score. Both run on the full frontal image. [BioViL-T model card and training data](https://huggingface.co/microsoft/BiomedVLP-BioViL-T)

Use the original NIH training, validation and test manifests. Before comparing on the official test, choose the epoch and any ensemble on validation and record the selection. Compare the full 14-finding macro average, average precision, and each finding separately. The official benchmark has been reused across project rounds. A new matched cohort would provide an independent confirmation.
