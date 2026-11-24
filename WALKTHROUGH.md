# Follow one chest X-ray prediction

A single frontal image goes through two scoring pipelines. The final model reports a score for each of 14 findings. The image below is a generated pattern and the scores came from the same model used for the reported benchmark.

![Synthetic chest X-ray, its resized input and four of the model's finding scores](examples/workflow.png)

## 1. Open the input

[The example image](examples/phantom.png) is a grayscale PNG. The experiment uses frontal radiographs. The model can report multiple findings for the same image, so it produces 14 separate scores.

## 2. Prepare and score the image

The BioViL-T branch resizes the image to 384 × 384 pixels and combines whole-image evidence with a branch that scores smaller areas. The reference uses the previously trained system. The locked model weights the two raw scores, or **logits**, at **75% BioViL-T** and **25% reference** for each finding. Validation fits the conversion from these scores to calibrated values.

For effusion, the weighted logit is **−1.5426**, the calibrated score is **0.1667** and the validation threshold is **0.2686**. The JSON therefore gives `threshold_crossed: false` for this finding on the synthetic input. [All 14 scores](examples/biovil-ensemble.json) · [Component and calibration trace](examples/frontal-trace.json) · [Where blur changed the model score](WHERE-THE-MODEL-LOOKS.md)

## 3. Run and check the example

Use `$p`, `$experiment`, `$search`, `$hf` and `$evaluation` from the [BioViL-T workflow](RESEARCH.md#reproduce-the-selected-model). Run the commands below from the repository root.

```powershell
& $p -m medimage.auc_predict --selection "$evaluation/selection.json" --work $experiment `
    --previous $search --original artifacts --weights $hf --image examples/phantom.png `
    --output "$evaluation/phantom.json" --trace "$evaluation/phantom-trace.json"
& $p -m medimage.workflow_figure chest --examples examples `
    --trace "$evaluation/phantom-trace.json" --output "$evaluation/phantom-workflow.png"
```

Open the output and compare two findings. Each has its own threshold. Check that there are exactly 14 entries and that every calibrated score lies between 0 and 1. Freshly trained weights can produce different values.

## 4. Understand the test result

| What can go wrong | How the experiment addresses it |
| --- | --- |
| The same patient or image appears in both partitions | Group patients and connect groups linked by identical image pixels |
| Frequent findings dominate one overall score | Give each of the 14 findings equal weight in macro AUROC |
| A high validation score leads to a poor test choice | Freeze the selected ensemble and thresholds before scoring the official test |
| A rare finding can have high AUROC with many false positives | Report average precision and per-finding results as well |

The locked ensemble reached **0.8103 macro AUROC** and **0.2804 macro average precision** across 25,596 official test radiographs. AUROC describes ranking. Average precision reflects the tradeoff between finding positive examples and keeping predicted positives reliable. The model ranked emphysema better than pneumonia, and the labels were derived from radiology reports. [Full benchmark report](RESEARCH.md)
