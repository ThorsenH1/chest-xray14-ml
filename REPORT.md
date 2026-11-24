# Previous model-search report

The [follow-up experiment](AUROC.md) raised macro AUROC to 0.8025 on the same official test cohort. The results below document the prior search.

The selected configuration is `incumbent+adapted-incumbent-224-0.50` with weights {"incumbent-global": 0.5, "adapted-incumbent-224": 0.5}.

| Metric | Initial | Selected | 95% paired delta interval |
| --- | ---: | ---: | ---: |
| AUROC | 0.7787 | 0.7900 | [+0.0098, +0.0130] |
| Average precision | 0.2386 | 0.2487 | [+0.0074, +0.0146] |
| Brier score | 0.0592 | 0.0587 | — |
| Expected calibration error | 0.0106 | 0.0110 | — |

The selected AUROC has a patient-cluster 95% CI of [0.7821, 0.7967] on 25,596 test units. The paired comparison uses 400 patient-cluster bootstrap draws. The test cohort was also used for the initial benchmark. The new model, blend, and calibration were locked before this round's test scoring.

## Validation candidates

| Model or blend | Selection score | AUROC | Average precision |
| --- | ---: | ---: | ---: |
| `incumbent+adapted-incumbent-224-0.50` | 0.52710 | 0.82711 | 0.22710 |
| `incumbent+top-two-new` | 0.52642 | 0.82715 | 0.22568 |
| `incumbent+adapted-incumbent-224-0.75` | 0.52569 | 0.82775 | 0.22363 |
| `incumbent+adapted-incumbent-224-0.25` | 0.52299 | 0.82242 | 0.22355 |
| `incumbent+adapted-effb0-224-0.25` | 0.52071 | 0.82043 | 0.22099 |
| `incumbent+dino-224-0.25` | 0.52067 | 0.82142 | 0.21993 |
| `incumbent+effb0-224-0.25` | 0.51996 | 0.82053 | 0.21939 |
| `incumbent+dino-224-0.50` | 0.51940 | 0.82260 | 0.21621 |
| `adapted-incumbent-224` | 0.51940 | 0.82340 | 0.21539 |
| `incumbent+r50-224-0.25` | 0.51914 | 0.82035 | 0.21794 |
| `incumbent+effb0-160-0.25` | 0.51853 | 0.81962 | 0.21744 |
| `incumbent+adapted-effb0-224-0.50` | 0.51712 | 0.81800 | 0.21624 |
| `incumbent+effb0-224-0.50` | 0.51676 | 0.82059 | 0.21294 |
| `top-two-new` | 0.51576 | 0.82180 | 0.20972 |
| `incumbent-global` | 0.51545 | 0.81515 | 0.21576 |
| `incumbent+r50-224-0.50` | 0.51387 | 0.81846 | 0.20928 |
| `incumbent+effb0-160-0.50` | 0.51376 | 0.81928 | 0.20824 |
| `incumbent+dino-224-0.75` | 0.50390 | 0.81171 | 0.19609 |
| `incumbent+effb0-224-0.75` | 0.50151 | 0.80935 | 0.19367 |
| `incumbent+adapted-effb0-224-0.75` | 0.49870 | 0.80325 | 0.19414 |
| `incumbent+effb0-160-0.75` | 0.49472 | 0.80623 | 0.18321 |
| `incumbent+r50-224-0.75` | 0.49259 | 0.80183 | 0.18334 |
| `effb0-224` | 0.46737 | 0.77934 | 0.15540 |
| `dino-224` | 0.46651 | 0.77608 | 0.15694 |
| `adapted-effb0-224` | 0.46599 | 0.77364 | 0.15834 |
| `effb0-160` | 0.45449 | 0.76762 | 0.14136 |
| `r50-224` | 0.45156 | 0.76193 | 0.14120 |

## Search profile winners

| Profile | Epochs per refit | Head | Width | Learning rate | Weight decay | Dropout | Positive-weight exponent |
| --- | ---: | --- | ---: | ---: | ---: | ---: | ---: |
| `effb0-160` | 6 | attention | 224 | 0.0014 | 0.012 | 0.45 | 0.5 |
| `r50-224` | 7 | attention | 128 | 0.001 | 0.007 | 0.3 | 0.5 |
| `effb0-224` | 8 | attention | 128 | 0.001 | 0.007 | 0.3 | 0.5 |
| `dino-224` | 15 | mlp | 256 | 0.0015 | 0.008 | 0.35 | 0.5 |

## Benchmark breakdown

| Finding or endpoint | Positive units | Initial AUROC | Selected AUROC | Initial AP | Selected AP |
| --- | ---: | ---: | ---: | ---: | ---: |
| Atelectasis | 3,279 | 0.7501 | 0.7568 | 0.3124 | 0.3247 |
| Cardiomegaly | 1,069 | 0.8675 | 0.8745 | 0.3166 | 0.3175 |
| Effusion | 4,658 | 0.8160 | 0.8207 | 0.4888 | 0.4970 |
| Infiltration | 6,112 | 0.6815 | 0.6918 | 0.3795 | 0.3867 |
| Mass | 1,748 | 0.7657 | 0.7753 | 0.2521 | 0.2639 |
| Nodule | 1,623 | 0.6932 | 0.7097 | 0.1737 | 0.1873 |
| Pneumonia | 555 | 0.6804 | 0.6901 | 0.0428 | 0.0437 |
| Pneumothorax | 2,665 | 0.8354 | 0.8461 | 0.3666 | 0.3836 |
| Consolidation | 1,815 | 0.7227 | 0.7334 | 0.1453 | 0.1534 |
| Edema | 925 | 0.8347 | 0.8439 | 0.1610 | 0.1704 |
| Emphysema | 1,093 | 0.8672 | 0.8831 | 0.3358 | 0.3517 |
| Fibrosis | 435 | 0.7857 | 0.7987 | 0.0901 | 0.0975 |
| Pleural Thickening | 1,143 | 0.7454 | 0.7581 | 0.1225 | 0.1278 |
| Hernia | 86 | 0.8559 | 0.8782 | 0.1526 | 0.1765 |

| Subgroup | Units | AUROC | Average precision | Brier |
| --- | ---: | ---: | ---: | ---: |
| AP | 14,500 | 0.7572 | 0.2115 | 0.0616 |
| PA | 11,096 | 0.7866 | 0.2592 | 0.0548 |
| female | 10,714 | 0.7883 | 0.2479 | 0.0589 |
| male | 14,882 | 0.7905 | 0.2499 | 0.0585 |
| 18 to 49 | 11,930 | 0.7916 | 0.2418 | 0.0559 |
| 50 to 69 | 10,863 | 0.7818 | 0.2507 | 0.0606 |
| 70 to 100 | 1,748 | 0.7617 | 0.2326 | 0.0628 |

## Search protocol

Each of four encoder and resolution profiles starts with 12 recipes. The resource rungs use [3, 8, 15] epochs. Inner ranking uses 64,517 fit units and 11,567 held-out units grouped by patient. Each profile winner is refitted with two seeds on 76,084 units. Final choice and calibration use 10,411 validation units.

The radiography blend combines logits after correcting for positive-class weighting. The displayed curves use a fixed 101-point grid. Mammography reliability bins appear when they have at least eight samples. Aggregate metrics and trial summaries are in [metrics.json](metrics.json).
