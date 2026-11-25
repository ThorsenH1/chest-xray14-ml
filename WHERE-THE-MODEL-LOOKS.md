# See which parts of the image change the score

![A faint synthetic shadow, its before and after zoom, and measured model responses](examples/where-model-looked.png)

1. The turquoise box shows where a faint bright patch was added to the generated chest image.
2. The two zooms show the same patch of the image before and after the edit.
3. The last panel tests the trained **BioViL-T effusion scorer**. Each probe blurs one small region of the processed image, runs the model again and records how the score moves.

**Red** shows a region where blurring lowered the score. **Blue** shows a region where blurring raised it. The edit changed the branch's internal score from **−1.150 to −1.128**. Several regions outside the added patch mattered to the model in this example. The map is a test of the score's response to blur in this specific image. [Edited synthetic input](examples/phantom-edited.png) · [All measured probes](examples/region-evidence.json)

The example measures an internal score from one component. The [prediction walkthrough](WALKTHROUGH.md) shows how the ensemble combines that component with the reference and calibrates the final output. The generated shadow is a visual teaching device.

## Check the model's strongest location against reference boxes

The NIH box file contains 984 annotations for eight findings across 880 images in the official test split. For each annotated finding, I took the strongest point in the BioViL-T branch's spatial score map and checked whether it fell inside a reference box for that finding.

| Rule for choosing a location | Inside a reference box |
| --- | ---: |
| BioViL-T spatial score maximum | **4.2%** |
| Uniformly chosen grid point, expected rate | 7.6% |
| Grid point closest to the image center | 21.5% |

The patient-bootstrap interval for the BioViL-T peak was **2.9–5.4%**. Results differ by finding. The peak was inside the marked area on **12.4%** of effusion examples and **10.2%** of pneumothorax examples.

This test checks the location of one internal score maximum on annotated positive images. The blur experiment above shows what changes a score when the image is altered. Use the dataset box as the placement reference when comparing these views.

Reproduce the peak audit with the locked BioViL-T model from [the research report](RESEARCH.md). Use the original NIH image folder and its box file.

```powershell
& $p -m medimage.chest_region_audit --root $data --cohort $cohort --boxes "$data/BBox_List_2017.csv" `
    --selection "$evaluation/selection.json" --work $experiment --previous $search --weights $hf `
    --output "$evaluation/localization-audit.json"
```

## Reproduce the test

Use the trained BioViL-T run and the [frozen ensemble selection](RESEARCH.md). Omitting an input file regenerates the same phantom. To study a local frontal image, add `--image` with its path and keep the resulting figure alongside your local data.

```powershell
& $p -m medimage.visual_explain --selection "$evaluation/selection.json" --work $experiment `
    --weights $hf --component biovil384 --label Effusion --output "$evaluation/region-probe"
```
