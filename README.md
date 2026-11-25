# Chest X-ray14 Multi-Label Classification

Group-aware training and calibrated prediction for 14 findings on frontal chest radiographs.

**0.8350 macro AUROC** and **0.3154 macro average precision** on 25,596 official test images. The 95% patient-bootstrap AUROC interval is 0.8291–0.8401.

The [research report](RESEARCH.md) shows how the selected ensemble weights BioViL-T, ConvNeXt and CXR-CLIP separately for each finding. It includes the validation ranking, paired test comparison and reproduction steps. [Original model search](REPORT.md) · [Search trajectories](figures/search.png)

## Start with one prediction

See how a synthetic chest X-ray becomes 14 finding scores. The [walkthrough](WALKTHROUGH.md) follows the image through the three model components, explains the thresholds and shows how to check the output locally. [See which regions changed its effusion score](WHERE-THE-MODEL-LOOKS.md)

![One example chest X-ray, its resized input and four prediction scores](examples/workflow.png)

## Benchmark

![Finding-level test AUROC with equal weights and with weights fitted per finding](figures/ranking-comparison.png)

Fitting a separate blend per finding increased macro AUROC by 0.00115 over the previous equal-weight model on the same test set. [See the confidence intervals and how the weights were selected](RESEARCH.md#fit-a-different-blend-for-each-finding)

## Dataset

| Partition | Images | Patient IDs |
| --- | ---: | ---: |
| Train | 76,084 | 24,643 |
| Validation | 10,411 | 3,361 |
| Official test | 25,596 | 2,797 |

The cohort groups patient IDs linked by identical images and retains the official NIH test list. Labels are report-derived. Macro metrics give rare findings the same top-level weight as common findings. [NIH release](https://nihcc.app.box.com/v/ChestXray-NIHCC) · [Dataset paper](https://arxiv.org/abs/1705.02315)

## Workflow

```mermaid
flowchart LR
    A[Frontal radiograph] --> B[Group-aware cohort]
    B --> C[Four transfer profiles]
    C --> D[Inner search and refits]
    D --> E[Validation selection and calibration]
    E --> F[14 scores]
```

The project uses a pinned [shared training engine](https://github.com/ThorsenH1/medimage-ml-lab) and has a task-specific runner. The published artifact contains source, aggregate benchmark results, and synthetic examples. Dataset files, checkpoints, and individual evaluation predictions stay in local storage.

## Reproduce the experiment

Use Python 3.11, PowerShell 7, and a CUDA-capable GPU. The original workstation used an RTX 2080 SUPER with 8 GiB VRAM.

```powershell
python -m venv .venv
$p = './.venv/Scripts/python.exe'
& $p -m pip install torch==2.14.0+cu126 torchvision==0.29.0+cu126 --index-url https://download.pytorch.org/whl/cu126
& $p -m pip install -r requirements.txt
```

Set the data and pretrained weight locations for your machine. The initial ResNet-18 weight directory uses `hub/checkpoints/resnet18-f37072fd.pth`. The Hugging Face cache uses `refs/main` and `snapshots/<revision>/model.safetensors`. Use the Hugging Face snapshots for `timm/efficientnet_b0.ra_in1k`, `timm/resnet50.a1_in1k`, and `timm/vit_small_patch14_dinov2.lvd142m`. [Initial ResNet-18 weights](https://download.pytorch.org/models/resnet18-f37072fd.pth)

```powershell
$data = 'D:/datasets/chest'
$torchCache = 'D:/models/torch'
$hf = 'D:/models/huggingface/hub'
pwsh -File scripts/Run-Project.ps1 -Task chest -Python $p -DataRoot $data -InitialWeights $torchCache -WeightRoot $hf
```

The runner writes the initial experiment under `artifacts/` and the extended search under `search-artifacts/`. For a larger volume, supply `-Artifacts` and `-Search` and use matching paths at inference. The prepared caches and training stages can resume from saved boundaries.

## Predict from raw input

Train the [three component models](RESEARCH.md#reproduce-the-selected-three-model-ensemble) and make the [validation lock with per-finding weights](RESEARCH.md#fit-a-different-blend-for-each-finding).

```powershell
& $p -m medimage.auc_predict --selection "$rankEvaluation/selection.json" --work $experiment --previous $search --original artifacts --weights $hf --image 'C:/images/frontal.png' --output "$rankEvaluation/chest-prediction.json"
```

The [example](examples/README.md) runs the selected local models on deterministic synthetic inputs. This is a research and education project.
