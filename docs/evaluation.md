# Evaluation

SAID evaluates all 78 full recordings in the DCASE2026 Task 3 development-test
split: 30 Sony recordings and 48 TAU recordings.

## Metric versions

The official evaluator changed during the challenge. The short labels used
below are defined here before their first use:

| Label | Official definition |
|---|---|
| **(4.2) COCO AP** — **2026-04-02**, [`84b2cd1`](https://github.com/iranroman/DCASE2026_Task3_SAISELD_baseline/commit/84b2cd155c9ee0c2176f386db54541ffd75312b2) | 180×360 spherical Gaussian masks, σ = 6°, 10% peak threshold, and COCO segmentation AP at IoU 0.50:0.95. |
| **(6.30) Soft-IoU / Macro Pearson** — **2026-06-30**, [`d4df662`](https://github.com/iranroman/DCASE2026_Task3_SAISELD_baseline/commit/d4df66251f39e34bc0157be93858e5a68ec9d7c4) | 100×50 interpolated soft maps, soft-IoU AP at 0.25, 0.50, and 0.75, class-macro soft-map Pearson r, and correct zero AP when a class has ground truth but no detection. |

The paper uses the versions below:

| Paper column | Version |
|---|---|
| Macro mAP | **(4.2)**, averaged over 13 classes |
| Mask AP | **(4.2)**, after merging all classes into `All classes` |
| Macro Pearson r | **(6.30)** |
| Macro Class-F1 | SAID diagnostic: class F1 after 20° spatial matching |

The (4.2) evaluator also contains an older Pearson calculation. The paper does
not use it. Pearson r always comes from (6.30).

Complete full-recording development-test results:

| Paper checkpoint | Macro mAP (4.2) | Mask AP (4.2) | Macro mAP (6.30) | Class-agnostic AP (6.30) | Macro Pearson r (6.30) | Macro Class-F1 |
|---|---:|---:|---:|---:|---:|---:|
| SAID (PaSST) | **0.120150** | **0.237972** | 0.124514 | **0.197483** | 0.426790 | 0.388488 |
| SAID (AudioMAE) | 0.113441 | 0.228684 | **0.124763** | 0.193225 | **0.430736** | **0.395145** |

The AP values differ because (4.2) and (6.30) are different official metric
definitions applied to the same predictions.

## Download the development data

Download these two files from the official
[STAIRS26 record](https://zenodo.org/records/18171005):

- [`32ch_audio_dev.zip`](https://zenodo.org/api/records/18171005/files/32ch_audio_dev.zip/content)
- [`labels_dev.zip`](https://zenodo.org/api/records/18171005/files/labels_dev.zip/content)

Extract both archives into one directory:

```bash
mkdir -p "$HOME/datasets/dcase2026_task3"
unzip 32ch_audio_dev.zip -d "$HOME/datasets/dcase2026_task3"
unzip labels_dev.zip -d "$HOME/datasets/dcase2026_task3"
```

Expected layout:

```text
$HOME/datasets/dcase2026_task3/
├── eigen_dev/
│   ├── dev-test-sony/*.wav
│   └── dev-test-tau/*.wav
└── labels_dev/
    ├── dev-test-sony/*_std.json
    └── dev-test-tau/*_std.json
```

## Run the paper evaluation

```bash
said evaluate "$HOME/datasets/dcase2026_task3" \
  --model said_passt \
  --add-previous-metrics
```

The example includes `--add-previous-metrics` because it reproduces the paper
table.

The command writes to `said_evaluation/` by default:

```text
said_evaluation/
├── evaluation_manifest.json
├── inference_outputs/
│   └── 78 *_inference.json files
├── metrics.json
└── evaluator.json
```

The uncompressed PaSST prediction files used for the paper average 369.58 MB
per recording and require approximately 28.8 GB for all 78 recordings. An
interrupted inference run can be continued with the same command: SAID checks
the model, dataset, protocol, and hashes of completed predictions before
resuming.

| Command | Metrics produced |
|---|---|
| `said evaluate ...` | Current (6.30) Macro mAP, class-agnostic AP, Macro Pearson r, and Macro Class-F1 |
| `said evaluate ... --add-previous-metrics` | All default metrics, plus (4.2) Paper Macro mAP and Paper Mask AP |

`--add-previous-metrics` adds the two (4.2) AP values. It does not change
inference or Pearson r.

The evaluator comes from the
[official DCASE2026 Task 3 repository](https://github.com/iranroman/DCASE2026_Task3_SAISELD_baseline).

## Output fields

With `--add-previous-metrics`, `metrics.json` contains:

```json
{
  "official_macro_map": 0.124514075016962,
  "official_class_agnostic_ap": 0.197482567736491,
  "official_macro_pearson_r": 0.426790037524333,
  "matched_class_macro_f1": 0.388487929954242,
  "paper_coco_macro_map": 0.120150225347623,
  "paper_coco_mask_ap": 0.237972219425958
}
```

| JSON field | Meaning |
|---|---|
| `official_macro_map` | (6.30) current official Macro mAP |
| `official_class_agnostic_ap` | (6.30) AP after merging all classes |
| `official_macro_pearson_r` | (6.30) current official Macro Pearson r |
| `matched_class_macro_f1` | SAID 20° matched Macro Class-F1 |
| `paper_coco_macro_map` | (4.2) paper Macro mAP |
| `paper_coco_mask_ap` | (4.2) paper Mask AP with one `All classes` category |

Without `--add-previous-metrics`, the final two fields are absent.

### Macro Class-F1

Macro Class-F1 separates class assignment from map-overlap AP. At each frame,
ground-truth and predicted sources are matched without using their classes:
the cost is the great-circle distance between the peak points of their maps,
Hungarian assignment finds the minimum-cost pairs, and only pairs within 20°
are retained. The class confusion matrix is accumulated across all
recordings. Unmatched sources do not enter this diagnostic. F1 is computed for
every reference class present in the evaluated data; a class with no retained
support receives zero, and the reported value is the macro average.

## Score existing predictions

Inference does not need to be repeated:

```bash
said evaluate "$HOME/datasets/dcase2026_task3" \
  --predictions said_evaluation/inference_outputs \
  --output said_evaluation_rescored \
  --add-previous-metrics
```

Compressed DCASE JSON can be evaluated in the same way by passing its output
directory to `--predictions`.

To export predictions without computing metrics:

```bash
said evaluate "$HOME/datasets/dcase2026_task3" \
  --model said_passt \
  --predictions-only
```

## Paper inference protocol

- selected **1-based Eigenmike capsule numbers** `[6, 10, 26, 22]` at 48 kHz;
- two-second windows, retaining the first 20 of 21 detector positions;
- 10 predictions per second;
- at most four slots per frame with confidence at least 0.05;
- map points at least 10% of the corresponding map peak;
- scores and energy rounded to six decimal places;
- zero-based 13-class taxonomy;
- `x_dcase = (179 - x_model) mod 360`.

The evaluated frame count is `max(metadata_frame_index) + 1` from each label
file. Predictions beyond that interval are not emitted.

## Exact official revisions

| Date | Commit | Change |
|---|---|---|
| **(4.2)** | [`84b2cd1`](https://github.com/iranroman/DCASE2026_Task3_SAISELD_baseline/commit/84b2cd155c9ee0c2176f386db54541ffd75312b2) | COCO Mask AP used by the paper |
| 5.18 | [`2bbbe41`](https://github.com/iranroman/DCASE2026_Task3_SAISELD_baseline/commit/2bbbe41af0815c84122d1df5247ec25d483acbb1) | Replaced COCO masks with interpolated soft maps and introduced the later Pearson framework |
| 5.28 | [`d47b751`](https://github.com/iranroman/DCASE2026_Task3_SAISELD_baseline/commit/d47b751548a303cad109a2affd5934a12ee2d65e) | Set soft-IoU thresholds to 0.25, 0.50, and 0.75 |
| **(6.30)** | [`d4df662`](https://github.com/iranroman/DCASE2026_Task3_SAISELD_baseline/commit/d4df66251f39e34bc0157be93858e5a68ec9d7c4) | Corrected AP handling for ground truth with no detection |

The default command downloads the official repository's current `main`, which
is (6.30) at this release. The optional (4.2) AP source is fixed to `84b2cd1`.

Sources:

```text
Repository:     https://github.com/iranroman/DCASE2026_Task3_SAISELD_baseline
Current (6.30): https://raw.githubusercontent.com/iranroman/DCASE2026_Task3_SAISELD_baseline/main/evaluate.py
Paper AP (4.2): https://raw.githubusercontent.com/iranroman/DCASE2026_Task3_SAISELD_baseline/84b2cd1/evaluate.py
```

The downloaded source URL and SHA256 are recorded in `evaluator.json`. Metric
workers process recordings in parallel and merge them in recording order. The
official metric formulas and parameters are unchanged. The evaluator's 20 MB
submission guard is checked separately by `said compress` and is not applied
when reproducing local scores.

Inference progress is recorded after each completed recording. Repeating an
interrupted full-evaluation command validates the checkpoint, dataset,
protocol, and completed prediction hashes before continuing inference. Metric
workers display per-recording progress but recompute the metric stage after an
interruption.
