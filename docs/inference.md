# Inference

## Command line

FFmpeg must be available on `PATH` for `said demo` and `said infer --visual`.

Select either complete paper model. On first use, SAID downloads its checkpoint
from the official Hugging Face repository and verifies its recorded size,
SHA256, schema, shapes, and dtypes before deserialization:

```bash
said demo --model said_passt
said demo --model said_audiomae
```

Generate the class-agnostic Audio2Sph comparisons with:

```bash
said demo --model audio2sph
```

This writes `said_demo_output/audio2sph_demo_1.mp4` through
`audio2sph_demo_4.mp4`. Each video merges the official per-source Ground Truth
maps by a pixelwise maximum on the left and shows the Audio2Sph + Panoramic
Decoder prediction on the right. Both sides use one fixed zero-to-one display
scale and contain no class legend.

The command writes four synchronized comparison videos:

```text
said_demo_output/
├── said_passt_demo_1.mp4 ... said_passt_demo_4.mp4
├── said_audiomae_demo_1.mp4 ... said_audiomae_demo_4.mp4
└── audio2sph_demo_1.mp4 ... audio2sph_demo_4.mp4
```

Each command adds only its four model-specific files. Existing demos from the
other models remain in the same directory, and an existing file is never
overwritten.

The generated files use these fixed excerpts:

| Video | Domain | Recording | Interval |
|---|---|---|---:|
| `*_demo_1.mp4` | TAU | `fold4_room8_mix003` | 150--170 s |
| `*_demo_2.mp4` | TAU | `fold4_room10_mix007` | 50--70 s |
| `*_demo_3.mp4` | Sony | `fold4_room24_mix002` | 20--40 s |
| `*_demo_4.mp4` | Sony | `fold4_room23_mix003` | 0--20 s |

Each complete-SAID file is 1920×600; each Audio2Sph + Panoramic Decoder file
is 1920×568. All generated files are 20 seconds with H.264 video and 48 kHz
stereo AAC audio. The synchronized 360-degree video displays the resulting
acoustic maps in the recorded scene, with Ground Truth on the left and
Prediction on the right. Complete-SAID videos share a fixed per-scene category
legend, colors, time labels, coordinate conversion, and overlay scale across
both views. Individual maps are peak-normalized for display, while inference
archives and reported metrics retain their original values.

The source and wheel include these four licensed excerpts: the four selected
Eigenmike channels at the official 24 kHz sample rate, the corresponding
20-second acoustic-map labels, and synchronized panorama video. The repository
README presents the generated SAID (PaSST) comparisons for Demo 1 and Demo 3;
`said demo` generates all four comparison MP4 files locally.

PaSST is selected by default. The same model selector applies to recordings:

```bash
said infer recording.wav --model said_passt
said infer recording.wav --model said_audiomae
said infer recording.wav --model audio2sph
```

The default location is the repository-root `checkpoints/` directory.
`SAID_CHECKPOINT_CACHE` changes that location. `SAID_CHECKPOINT_BASE_URL`
selects an alternative release mirror. `--checkpoint PATH` loads an explicit
local asset instead of the selected paper checkpoint.
Downloads use a cross-platform concurrent-process lock, retain resumable
partial files, publish atomically, and preserve any existing completed file
for explicit integrity diagnosis.

The input is either the four Eigenmike capsule signals used by SAID, in the
order `[6, 10, 26, 22]`, or a complete 32-channel Eigenmike recording. These
are **1-based Eigenmike capsule numbers**. The loader selects the four signals
and resamples the recording to 48 kHz.

SAID processes complete recordings in two-second windows. Every window
produces 21 internal detector positions; the endpoint is omitted when windows
are concatenated, yielding a continuous 10 fps output. A partial final window
is zero-padded. Output frame `k` begins at `k / 10` seconds, and every frame
whose start is earlier than the recording duration is retained.

Audio2Sph inference uses the same two-second segmentation and boundary rule at
its native 100 Hz time base. Each window produces 201 positions; the endpoint
is omitted before concatenation, yielding 200 non-overlapping frame starts per
two-second window. Its default `audio2sph-prediction-json-v1` output records
one class-agnostic annotation per 100 Hz frame. Each annotation contains its
frame index and spherical points at or above 10% of the map peak. The schema
records that category IDs and class confidence are not defined for Audio2Sph.

All three public model routes write one JSON file by default:

```text
said_output/
└── recording_inference.json
```

For complete SAID, each annotation contains its 10 Hz frame index, instance
ID, zero-based DCASE class ID, confidence, and spherical acoustic-map points.
This is standard DCASE JSON and is accepted by the compression command. It can
also be scored by the evaluation command when its recording filename matches a
DCASE development-test label. Audio2Sph uses the class-agnostic schema
described above. In both schemas, values below 10% of each map's peak are
omitted; the paper's grid compression is not applied.

Use the optional NumPy archive when exact float32 maps are required:

```bash
said infer recording.wav --model said_passt --format npz
said infer recording.wav --model audio2sph --format npz
```

The complete SAID command creates a lossless `said-prediction-archive-v1`
directory. It is distinct from standard DCASE JSON and from the paper's lossy
JSON compression method. Its `manifest.json` records the model, class
taxonomy, frame rate, map dimensions, checkpoint SHA256, Class Feature
Encoder, recording domain, frame-time rule, selection settings, and ordered
chunk files. Each losslessly compressed NumPy chunk contains:

- `frame_index` and `slot_index`;
- zero-based `class_id`;
- paper `confidence`;
- the corresponding 180×360 `refined_map` stored losslessly as float32.

The Audio2Sph command creates an
`audio2sph-prediction-archive-v1` directory. Its manifest records the model,
checkpoint, 100 Hz time base, map dimensions, frame-time rule, and ordered
chunks. Each chunk contains one float32 `map` array with shape
`[frames, 180, 360]`; class IDs, confidence, slots, and recording-domain
metadata do not apply to this class-agnostic route.

The map row runs from +90° elevation at the top to −90° at the bottom. For a
map column `x_sph`, the standard DCASE horizontal coordinate is
`(179 - x_sph) mod 360`. The DCASE exporter applies this conversion.

Complete SAID retains candidates with confidence at least 0.05 and at most
four slots per frame by default. These are the paper evaluation selection
settings, rather than additional model layers. They can be changed without
changing the model forward pass:

```bash
said infer recording.wav \
  --model said_passt \
  --score-threshold 0.10 \
  --max-sources-per-frame 6
```

SAID (PaSST) uses recording-domain embeddings learned from the Sony and TAU
DCASE subsets. Sony is the documented command-line default and does not imply
automatic domain detection. TAU recordings select their embedding with:

```bash
said infer recording.wav \
  --model said_passt \
  --recording-domain tau
```

## Optional visualization

Pass a synchronized equirectangular video to render the prediction with the
same coordinate conversion, class colors, heat-map overlay, time labels,
audio handling, and FFmpeg encoder used by `said demo`:

```bash
said infer recording.wav \
  --model said_passt \
  --visual panorama.mp4
```

The output directory then contains both `recording_inference.json` and
`recording_prediction.mp4`. Complete SAID displays its labeled predictions;
Audio2Sph displays its class-agnostic acoustic field. Audio2Sph retains 100 Hz
numerical output while its MP4 is rendered at 10 fps. Combining `--visual`
with `--format npz` retains the lossless archive alongside the MP4. Prediction
is computed once; visualization reuses that result.

## Python API

```python
from said import RecordingDomain, SAID, load_paper_checkpoint
from said.inference import SAIDPredictor, load_eigenmike_audio

model = SAID(class_feature_encoder="passt")
load_paper_checkpoint(
    model,
    "/path/to/said_passt.ckpt",
    class_feature_encoder="passt",
)

audio = load_eigenmike_audio("recording.wav")
predictor = SAIDPredictor(model, device="cuda")

for chunk in predictor.predict(audio, recording_domain=RecordingDomain.SONY):
    print(chunk.frame_start, chunk.frame_stop)
    print(chunk.refined_maps.shape)           # [frames,16,180,360]
    print(chunk.slot_class_ids.shape)         # [frames,16]
    print(chunk.slot_confidence.shape)        # [frames,16]
```

The class-agnostic API uses the published pretraining wrapper:

```python
from said import Audio2SphPretrainingModel, load_audio2sph_pretraining_checkpoint
from said.inference import Audio2SphPredictor, load_eigenmike_audio

model = Audio2SphPretrainingModel()
load_audio2sph_pretraining_checkpoint(model, "/path/to/audio2sph.ckpt")
audio = load_eigenmike_audio("recording.wav")
predictor = Audio2SphPredictor(model, device="cuda")

for chunk in predictor.predict(audio):
    print(chunk.frame_start, chunk.frame_stop, chunk.maps.shape)
```

The iterator returns each prediction window after it is computed and therefore
does not retain the complete set of predicted maps. The audio loader currently
loads the input recording before segmented inference.

## Reading outputs

The default output is ordinary JSON:

```python
import json

prediction = json.load(
    open("said_output/recording_inference.json", encoding="utf-8")
)
for annotation in prediction["annotations"]:
    frame = annotation["metadata_frame_index"]
    points = annotation["segmentation"]
```

Complete SAID annotations additionally contain `category_id`, `instance_id`,
and `score`. The Audio2Sph top-level schema identifies the file as
class-agnostic and records its 100 Hz time base.

An explicitly requested complete-SAID NPZ archive can be decoded with NumPy:

```python
import json
import numpy as np

manifest = json.load(open("said_output/manifest.json", encoding="utf-8"))
for record in manifest["chunks"]:
    with np.load("said_output/" + record["file"]) as chunk:
        frame_index = chunk["frame_index"]
        class_id = chunk["class_id"]
        confidence = chunk["confidence"]
        refined_map = chunk["refined_map"]
```

For an Audio2Sph NPZ archive, each chunk contains only the dense maps:

```python
import json
import numpy as np

manifest = json.load(open("said_output/manifest.json", encoding="utf-8"))
for record in manifest["chunks"]:
    with np.load("said_output/" + record["file"]) as chunk:
        acoustic_map = chunk["map"]  # [frames, 180, 360]
```
