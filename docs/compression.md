# Compression

Compression is an optional postprocessing step for DCASE JSON size
constraints. It operates on exported prediction files and has no model or
checkpoint dependency:

```bash
said compress said_evaluation/inference_outputs --output said_compressed
```

The default output of single-recording SAID inference is also accepted:

```bash
said compress said_output --output said_compressed
```

Both inputs contain standard complete-SAID `*_inference.json` files. The
class-agnostic Audio2Sph JSON schema has no DCASE category or confidence fields
and is outside this submission-size postprocessing route. NPZ archives
requested with `said infer --format npz` retain dense numerical maps and are
also outside this DCASE JSON postprocessing command.

The default `paper-table1` preset implements the evaluated Table 1 artifact.
For each acoustic map it first removes points below 10% of the map peak.
Confidence at least 0.20 selects a two-pixel grid and adds no boundary points.
Lower confidence selects a six-pixel grid and adds support points two pixels
from the low-energy boundary, with support energy equal to 12% of the peak.
Every detection and its metadata are retained.

Classes 2, 3, 5, and 10 have no point cap; their strongest point in each
selected grid cell is retained. Other classes use an 88-point cap. Maps that
already contain at most 88 points after thresholding and boundary support keep
those points. Larger maps first form grid candidates. If the grid leaves fewer
than 88 candidates, selection returns to all post-threshold and boundary
points. The cap then selects across high-, medium-, and low-energy bands while
favoring spatial spread, until at most 88 remain. Coordinates use three
decimals, energies use four, and integral coordinates are written as JSON
integers. The complete settings, input SHA256, and output SHA256 are recorded in
`compression_manifest.json`.

The size constraint is 20,000,000 bytes for each prediction JSON. A file that
exceeds the limit is recomputed with the recorded stricter setting: the
confidence split becomes 0.25, the low-confidence boundary offset becomes four
pixels, and the 88-point cap applies to every class. Point budgets of 64, 48,
and 32 are then tried in sequence if needed. The manifest stores every
attempted preset, its byte count, and the selected preset for each file.
Compression raises an error and publishes no output directory if all presets
remain above the limit. It never removes detections to satisfy the limit.

Table 1 uses the paper SAID (PaSST) checkpoint, decimal megabytes, and the
complete 78-recording development-test split:

| Prediction | Macro mAP (4.2) | Macro Pearson r (6.30) | Max. JSON | Avg. JSON |
|---|---:|---:|---:|---:|
| Original | 0.1202 | 0.4268 | 941.50 MB | 369.58 MB |
| Compressed | 0.1177 | 0.4487 | 18.70 MB | 8.08 MB |

The compressed Table 1 artifact used `paper-table1` for all 78 files; its
per-recording fallback count is zero.

Use the automatically downloaded official evaluator described in
[Evaluation](evaluation.md) to score the compressed directory without
repeating inference:

```bash
said evaluate "$HOME/datasets/dcase2026_task3" \
  --predictions said_compressed \
  --output said_compressed_evaluation \
  --add-previous-metrics
```

The separate output directory preserves the original evaluation record while
storing the compressed metrics and evaluator identity.
