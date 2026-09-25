<h1 align="center">SAID: Semantic Acoustic Imaging Detector for Sound Event Localization and Detection</h1>

<p align="center">
  Runbang Wang, Zining Liang, Yin Cao, and Qiuqiang Kong
</p>

<p align="center">
  <a href="https://huggingface.co/IN03X/SAID">Models</a> ·
  <a href="https://dcase.community/challenge2026/task-semantic-acoustic-imaging-for-sound-event-localization-and-detection-from-spatial-audio-and-audiovisual-scenes-results">DCASE Results</a> ·
  <a href="#demos">Demos</a> ·
  <a href="#evaluation">Evaluation</a> ·
  <a href="#training-and-data">Training</a> ·
  <a href="#documentation">Documentation</a>
</p>

<p align="center">
  <img src="https://huggingface.co/IN03X/SAID/resolve/main/docs/media/said_overview.png" alt="SAID overview: four-channel audio is encoded into panoramic features and decoded into separate labeled acoustic maps" width="100%">
</p>

## Overview

SAID predicts a separate **labeled acoustic map** for every active sound source
from four-channel spatial audio. Each 180 × 360 map represents the source
region, its acoustic energy, and its sound-event class. Audio2Sph organizes
audio features by direction, and Sph2Imaging decodes these panoramic features
into a variable set of source maps. The release includes the complete inference,
evaluation, training, and Online Scene Generation workflows described in the
paper.

Details: [Model Architecture](docs/model_architecture.md).

## Demos

The following 20-second examples show SAID (PaSST) and Audio2Sph + Panoramic
Decoder outputs. Ground Truth is shown on the left and Prediction on the right
with a shared timeline, colors, and display scale. The SAID videos also include
the sound-event class legend. All four MP4 files include synchronized audio.

### SAID (PaSST) — TAU scene

<video src="https://huggingface.co/IN03X/SAID/resolve/main/docs/media/said_demo_1.mp4" controls playsinline preload="metadata" width="100%"></video>

### SAID (PaSST) — Sony scene

<video src="https://huggingface.co/IN03X/SAID/resolve/main/docs/media/said_demo_3.mp4" controls playsinline preload="metadata" width="100%"></video>

### Audio2Sph + Panoramic Decoder — TAU scene

<video src="https://huggingface.co/IN03X/SAID/resolve/main/docs/media/audio2sph_demo_1.mp4" controls playsinline preload="metadata" width="100%"></video>

### Audio2Sph + Panoramic Decoder — Sony scene

<video src="https://huggingface.co/IN03X/SAID/resolve/main/docs/media/audio2sph_demo_3.mp4" controls playsinline preload="metadata" width="100%"></video>

Details: [Inference](docs/inference.md).

## Quick start

SAID supports Python 3.10 through 3.12. Create an environment, enter the project
directory, and install the package:

```bash
conda create -n said python=3.10 -y
conda activate said
cd /path/to/SAID
pip install -e .
```

FFmpeg must be available on `PATH` to generate demos or inference
visualizations.

Run the four packaged scenes with the default complete model:

```bash
said demo --model said_passt
```

Run SAID on an Eigenmike recording:

```bash
said infer recording.wav --model said_passt
```

The CLI downloads the selected checkpoint from
[Hugging Face](https://huggingface.co/IN03X/SAID) into the repository-root
`checkpoints/` directory on first use. `recording.wav` may contain the complete
32-channel Eigenmike signal or the four selected signals in the order of the
**1-based Eigenmike capsule numbers** `[6, 10, 26, 22]`. The default output is
readable DCASE JSON. Select the AudioMAE
variant with `--model said_audiomae`.

Details: [Inference](docs/inference.md).

## Results

### DCASE2026 Task 3 Track A evaluation set

| System | Rank | Macro mAP | Macro Pearson r |
|---|---:|---:|---:|
| CUHK (SAID) | **1** | **0.1080** | **0.3962** |

These are the
[official challenge results](https://dcase.community/challenge2026/task-semantic-acoustic-imaging-for-sound-event-localization-and-detection-from-spatial-audio-and-audiovisual-scenes-results)
for the submitted audio-only system.

### Full-recording development test set

`(4.2)` denotes the official evaluator dated 2026-04-02
([`84b2cd1`](https://github.com/iranroman/DCASE2026_Task3_SAISELD_baseline/commit/84b2cd155c9ee0c2176f386db54541ffd75312b2));
`(6.30)` denotes the official evaluator dated 2026-06-30
([`d4df662`](https://github.com/iranroman/DCASE2026_Task3_SAISELD_baseline/commit/d4df66251f39e34bc0157be93858e5a68ec9d7c4)).
The table evaluates all 78 full development-test recordings.

| Paper checkpoint | Macro mAP (4.2) | Mask AP (4.2) | Macro mAP (6.30) | Class-agnostic AP (6.30) | Macro Pearson r (6.30) | Macro Class-F1 |
|---|---:|---:|---:|---:|---:|---:|
| SAID (PaSST) | **0.120150** | **0.237972** | 0.124514 | **0.197483** | 0.426790 | 0.388488 |
| SAID (AudioMAE) | 0.113441 | 0.228684 | **0.124763** | 0.193225 | **0.430736** | **0.395145** |

The `(4.2)` AP columns reproduce the evaluator revision used by the paper;
`(6.30)` applies the current official evaluator to the same predictions.

Details: [Evaluation](docs/evaluation.md#metric-versions).

## Evaluation

Download and extract the official STAIRS26 development audio and labels as
described in [Evaluation](docs/evaluation.md#download-the-development-data),
then run:

```bash
said evaluate "$HOME/datasets/dcase2026_task3" \
  --model said_passt \
  --add-previous-metrics
```

The default metrics follow the current `(6.30)` official definition;
`--add-previous-metrics` adds the two `(4.2)` AP values reported by the paper.
The evaluator is acquired from the
[official DCASE2026 Task 3 repository](https://github.com/iranroman/DCASE2026_Task3_SAISELD_baseline).

Details: [Evaluation](docs/evaluation.md).

## Models and checkpoints

| Model | Role and output | Checkpoint |
|---|---|---|
| SAID (PaSST) | Complete class-aware SAID; default inference model | [`said_passt.ckpt`](https://huggingface.co/IN03X/SAID/resolve/main/said_passt.ckpt) · [card](docs/checkpoints/said_passt.md) |
| SAID (AudioMAE) | Complete class-aware SAID with AudioMAE Class Features | [`said_audiomae.ckpt`](https://huggingface.co/IN03X/SAID/resolve/main/said_audiomae.ckpt) · [card](docs/checkpoints/said_audiomae.md) |
| Audio2Sph + Panoramic Decoder | Class-agnostic panoramic acoustic maps | [`audio2sph.ckpt`](https://huggingface.co/IN03X/SAID/resolve/main/audio2sph.ckpt) · [card](docs/checkpoints/audio2sph.md) |

All published checkpoints are verified by filename, size, tensor schema, dtype,
and SHA256 before loading. They are released for non-commercial research use only
under the [SAID Model Weights Non-Commercial Research License 1.0](LICENSES/SAID-Model-Weights-NonCommercial-1.0.txt)
and all applicable upstream terms.

Details: [SAID (PaSST)](docs/checkpoints/said_passt.md),
[SAID (AudioMAE)](docs/checkpoints/said_audiomae.md), and
[Audio2Sph + Panoramic Decoder](docs/checkpoints/audio2sph.md).

## Training and data

The paper training workflow is:

```text
Audio2Sph pretraining
-> complete SAID training with SourceBank and Online Scene Generation
-> DCASE fine-tuning
```

The public recipes cover the Audio2Sph, SAID (PaSST), and SAID (AudioMAE)
routes. For example, after configuring the DCASE data path, fine-tune the
published PaSST model with:

```bash
said train --config configs/training/dcase_passt.yaml
```

Readers obtain the datasets from their rights holders and connect them through
the included adapters:

- DCASE2026 Task 3 recordings and labels for fine-tuning and evaluation;
- VCTK v0.80 for Audio2Sph pretraining;
- an authorized SourceBank manifest for class-labeled Online Scene Generation.

Details: [Training](docs/training.md), [Data](docs/data.md), and
[Configuration](docs/configuration.md).

## Documentation

- [Inference](docs/inference.md)
- [Evaluation](docs/evaluation.md)
- [Training](docs/training.md)
- [Configuration](docs/configuration.md)
- [Data](docs/data.md)
- [Model Architecture](docs/model_architecture.md)
- [Compression](docs/compression.md), an optional DCASE JSON
  post-processing utility

Command-specific options are available through `said COMMAND --help`.

## License

Original SAID software and documentation are released under the
[MIT License](LICENSE). The three published checkpoints are distributed for
non-commercial research. Third-party implementations, pretrained components,
datasets, demo media, and the official evaluator retain their corresponding
terms. The complete attribution and license boundaries are recorded in
[Third-party notices](THIRD_PARTY_NOTICES.md) and the
[model and asset license summary](LICENSES/README.md).

Commercial applications can train new models with the MIT-licensed SAID code
and independently obtained components and data whose licenses permit the
intended use. Commercial use of these checkpoints, including modified or
fine-tuned derivatives, is not permitted under the weights license.

## Citation

If you use SAID, please cite:

```bibtex
@inproceedings{wang2026said,
  title     = {{SAID}: Semantic Acoustic Imaging Detector for Sound Event
               Localization and Detection},
  author    = {Wang, Runbang and Liang, Zining and Cao, Yin and Kong, Qiuqiang},
  booktitle = {Proceedings of the Detection and Classification of Acoustic
               Scenes and Events 2026 Workshop},
  year      = {2026}
}
```

Machine-readable citation metadata is available in [CITATION.cff](CITATION.cff).

## Acknowledgements

The Online Scene Generation components build on the
[NESD release_v1.0](https://github.com/qiuqiangkong/nesd/tree/release_v1.0)
renderer. The rigid-sphere implementation credits Yin Cao and Qiuqiang Kong;
the renderer and shoebox image-source implementation credit Qiuqiang Kong and
CUHK. Detailed source revisions and notices are provided in
[THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).

This work was supported by the Innovation and Technology Fund (ITF), Hong Kong,
under Project ITS/301/24.
