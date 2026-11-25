# Follow one chest X-ray prediction

A single frontal image goes through three scoring pipelines. The final model reports a score for each of 14 findings. The image below is a generated pattern and the scores came from the same model used for the reported benchmark.

![Synthetic chest X-ray, its resized input and four of the model's finding scores](examples/workflow.png)

## 1. Open the input

[The example image](examples/phantom.png) is a grayscale PNG. The experiment uses frontal radiographs. The model can report multiple findings for the same image, so it produces 14 separate scores.

## 2. Prepare and score the image

Both BioViL-T and ConvNeXt read the image at 384 × 384 pixels. BioViL-T combines a whole-image score with a branch that scores smaller areas. CXR-CLIP reads the 224 × 224 version. The selected model gives the three raw scores, or **logits**, equal weight for each finding. The probability conversion and thresholds are fitted on validation.

In the example, the three effusion logits average to **−3.1398**. After calibration the score is **0.0317**. The validation threshold is **0.2579**, so the example returns `threshold_crossed: false`. [All 14 scores](examples/swin-convnext-ensemble.json) · [Component and calibration trace](examples/frontal-trace.json) · [Where blur changed BioViL-T's score](WHERE-THE-MODEL-LOOKS.md)

## 3. Run and check the example

Use `$p`, `$experiment`, `$search`, `$hf` and `$jointEvaluation` from the [three-model workflow](RESEARCH.md#reproduce-the-selected-three-model-ensemble). Run the commands below from the repository root.

```powershell
& $p -m medimage.auc_predict --selection "$jointEvaluation/selection.json" --work $experiment `
    --previous $search --original artifacts --weights $hf --image examples/phantom.png `
    --output "$jointEvaluation/phantom.json" --trace "$jointEvaluation/phantom-trace.json"
& $p -m medimage.workflow_figure chest --examples examples `
    --trace "$jointEvaluation/phantom-trace.json" --output "$jointEvaluation/phantom-workflow.png"
```

Open the output and compare two findings. Each has its own threshold. Check that there are exactly 14 entries and that every calibrated score lies between 0 and 1. Freshly trained weights can produce different values.

## 4. Understand the test result

| What can go wrong | How the experiment addresses it |
| --- | --- |
| The same patient or image appears in both partitions | Group patients and connect groups linked by identical image pixels |
| Frequent findings dominate one overall score | Give each of the 14 findings equal weight in macro AUROC |
| A high validation score leads to a poor test choice | Freeze the selected ensemble and thresholds before scoring the official test |
| A rare finding can have high AUROC with many false positives | Report average precision and per-finding results as well |

The locked ensemble reached **0.8339 macro AUROC** and **0.3138 macro average precision** across 25,596 official test radiographs. AUROC describes ranking. Average precision reflects the tradeoff between finding positive examples and keeping predicted positives reliable. The model ranked emphysema better than pneumonia, and the labels were derived from radiology reports. [Full benchmark report](RESEARCH.md)
