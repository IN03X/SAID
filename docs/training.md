# Training

All released training routes use one command:

```bash
said train --config configs/training/<recipe>.yaml
```

Before running a recipe, set the paths it needs in `configs/data.yaml`. Each
recipe is an independent entry point: it loads the published initialization
listed in its YAML unless that path is changed. Running one recipe does not
automatically change the initialization of another recipe.

## Choose a route

| Goal | Recipe | Local data required | Published initialization |
|---|---|---|---|
| Fine-tune SAID (PaSST) on DCASE | `dcase_passt.yaml` | DCASE development audio and labels | `said_passt.ckpt` |
| Fine-tune SAID (AudioMAE) on DCASE | `dcase_audiomae.yaml` | DCASE development audio and labels | `said_audiomae.ckpt` |
| Train SAID (PaSST) with SourceBank | `sourcebank_passt.yaml` | SourceBank manifest and local audio | `audio2sph.ckpt` and the PaSST Class Feature Encoder from `said_passt.ckpt` |
| Train SAID (AudioMAE) with SourceBank | `sourcebank_audiomae.yaml` | SourceBank manifest and local audio | `audio2sph.ckpt` and the AudioMAE Class Feature Encoder from `said_audiomae.ckpt` |
| Train Audio2Sph + Panoramic Decoder | `audio2sph.yaml` | Prepared VCTK v0.80 | none |

The quickest training check is DCASE fine-tuning. After setting
`dcase_recordings.root` in `configs/data.yaml`, run:

```bash
said train --config configs/training/dcase_passt.yaml
```

Replace `passt` with `audiomae` to use SAID (AudioMAE).

## Complete paper workflow

The paper training workflow is:

```text
Audio2Sph pretraining
→ complete SAID training with SourceBank and Online Scene Generation
→ DCASE fine-tuning
```

### 1. Train Audio2Sph

Install the Online Scene Generation dependencies, set the VCTK input and
prepared-output paths, prepare VCTK once, and then start training:

```bash
pip install -e '.[render]'

# Set simulated_scenes.vctk.source_root and prepared_root in configs/data.yaml.
said prepare --config configs/training/audio2sph.yaml
said train --config configs/training/audio2sph.yaml
```

The preparation step converts VCTK v0.80 into the validated two-second input
used by the recipe. The training recipe runs 2.5 million optimizer steps with
batch size one and writes Audio2Sph together with its training-time Panoramic
Decoder.

### 2. Train complete SAID with SourceBank

Set `simulated_scenes.sourcebank.manifest` and
`simulated_scenes.sourcebank.source_audio_root` in `configs/data.yaml`, then
run one Class Feature Encoder route:

```bash
said train --config configs/training/sourcebank_passt.yaml
# or
said train --config configs/training/sourcebank_audiomae.yaml
```

This route keeps the Audio2Sph encoder, removes the Panoramic Decoder, attaches
Sph2Imaging, and trains complete SAID on generated two-second scenes. It uses
exact 21-frame training targets. [Data](data.md) defines the SourceBank
manifest and permission contract.

### 3. Fine-tune complete SAID on DCASE

Set `dcase_recordings.root` in `configs/data.yaml`, then run the matching
recipe:

```bash
said train --config configs/training/dcase_passt.yaml
# or
said train --config configs/training/dcase_audiomae.yaml
```

DCASE fine-tuning consumes 20 official half-open 10 Hz frames per two-second
sample and discards only the detector endpoint during loss computation. It
does not merge adjacent official label frames.

## Pass a newly trained model to the next route

The published recipes start from published checkpoints. To continue a newly
trained workflow, edit the next recipe before launching it:

| Next recipe | Field to change | Example preceding output |
|---|---|---|
| `sourcebank_passt.yaml` or `sourcebank_audiomae.yaml` | `load_audio2sph_ckpt` | `../../runs/audio2sph_pretraining/latest.ema.ckpt` |
| `dcase_passt.yaml` | `load_said_ckpt` | `../../runs/said_sourcebank_passt/latest.ema.ckpt` |
| `dcase_audiomae.yaml` | `load_said_ckpt` | `../../runs/said_sourcebank_audiomae/latest.ema.ckpt` |

The SourceBank recipes also use `load_class_feature_encoder_ckpt` to extract
the selected frozen Class Feature Encoder. Its type must match
`class_feature_encoder: passt` or `class_feature_encoder: audiomae`.

Canonical published paths under `checkpoints/` are downloaded and verified on
first use. Any other path is treated as an explicit local checkpoint.

## Checkpoints and resume

Each run writes into its configured `training.output_directory`:

| File | Purpose |
|---|---|
| `latest.ema.ckpt` | Latest model weights; use this or a retained EMA milestone to initialize another recipe |
| `latest.training.pt` | Model, optimizer, scheduler, random state, step, and validation state for exact resume |
| `best.ema.ckpt`, `best.json` | Lowest validation loss when validation is enabled |
| `step-*.ema.ckpt`, `step-*.training.pt` | Retained milestones at `keep_every_steps` |
| `metrics.jsonl` | Complete machine-readable training and validation records |

Rerunning the same command resumes `latest.training.pt` automatically. Use
`--resume PATH` only to select another `.training.pt` from the same training
recipe and the same data configuration. The trainer verifies a fingerprint of
both YAML files before restoring the optimizer and random state.

Use a flat `.ema.ckpt` through `load_audio2sph_ckpt` or `load_said_ckpt` when
moving between routes. Validation-enabled Audio2Sph and SourceBank recipes
record `best.ema.ckpt`, while the handoff examples above deliberately use the
latest completed state. DCASE fine-tuning leaves validation disabled so the
development-test recordings remain reserved for full-recording evaluation.

The terminal reports step, loss, learning rate, throughput, ETA, validation,
and checkpoint events. [Configuration](configuration.md) defines every recipe
field; [Data](data.md) defines the required dataset layouts.
