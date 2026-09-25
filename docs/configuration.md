# Configuration

`said train --config configs/training/<recipe>.yaml` reads a training recipe.
The recipe points to `configs/data.yaml`, which contains local dataset paths
and Online Scene Generation settings. Relative paths are resolved from the
file that contains them.

## Training recipe fields

Every top-level field below is required:

| Field | Accepted value | Purpose |
|---|---|---|
| `schema` | `said-config-v1` | Public configuration version |
| `preset` | `paper` | Published architecture and objective settings |
| `model` | `said`, `audio2sph` | Complete SAID or Audio2Sph + Panoramic Decoder |
| `class_feature_encoder` | `passt`, `audiomae`, `null` | Class Feature Encoder; `null` for Audio2Sph |
| `data` | `dcase_recordings`, `simulated_scenes` | Selected section of the data configuration |
| `data_config` | path | Data configuration file |
| `load_said_ckpt` | path, `null` | Complete SAID initialization |
| `load_audio2sph_ckpt` | path, `null` | Audio2Sph initialization |
| `load_class_feature_encoder_ckpt` | path, `null` | Class Feature Encoder initialization when assembling SAID |
| `training` | mapping | Optimization, output, logging, validation, and checkpoint settings |

The initialization combinations are:

| Training object | Data | Required initialization | Result |
|---|---|---|---|
| `said` | `dcase_recordings` | `load_said_ckpt` | Fine-tune a complete SAID model |
| `said` | `simulated_scenes` | `load_audio2sph_ckpt` and `load_class_feature_encoder_ckpt` | Extract Audio2Sph and Class Feature Encoder components, assemble SAID, and train on SourceBank scenes |
| `audio2sph` | `simulated_scenes` | none, or `load_audio2sph_ckpt` | Train Audio2Sph + Panoramic Decoder from the beginning or initialize the complete pretraining model |

Complete-model initialization is mutually exclusive with component
initialization. For complete SAID,
`class_feature_encoder: passt` selects SAID (PaSST), and
`class_feature_encoder: audiomae` selects SAID (AudioMAE). Checkpoint keys,
tensor shapes, and dtypes are validated before training begins.

## Training settings

Fields from `output_directory` through `log_every_steps` are required. The
three validation fields are optional and their defaults are shown below. The
table also records the values used by the released recipes.

| Field | Released recipe value | Purpose |
|---|---|---|
| `output_directory` | route-specific path under `runs/` | Run outputs and checkpoints |
| `total_steps` | 2,500,000 for Audio2Sph; 500,000 for SAID | Optimizer updates |
| `batch_size` | `1` | Samples per optimizer update |
| `num_workers` | `8` | Data-loader workers |
| `seed` | route-specific integer | Model, sampling, and data-order seed |
| `device` | `auto` | CUDA when available, otherwise CPU |
| `learning_rate` | `1.0e-4` for simulated scenes; `1.5e-6` for DCASE | Main parameter-group learning rate; required, with `null` selecting the same route-specific values |
| `audio2sph_learning_rate` | `2.5e-6` | Audio2Sph parameter group during SourceBank SAID training; retained but unused by the Audio2Sph and DCASE recipes |
| `warmup_steps` | `1,000` | Linear warmup duration |
| `minimum_learning_rate_ratio` | `0.1` | Final cosine-schedule ratio |
| `ema_decay` | `0.999` | Exponential-moving-average decay |
| `save_every_steps` | `10,000` | Latest checkpoint cadence |
| `keep_every_steps` | `100,000` | Retained milestone cadence |
| `log_every_steps` | `100` | Terminal and `metrics.jsonl` cadence |
| `validate_every_steps` | `10,000` for simulated scenes; `null` for DCASE | Optional validation cadence; default `null` |
| `validation_batches` | `16` for simulated scenes; `0` for DCASE | Optional fixed validation batches; default `0` |
| `selection_metric` | `validation_loss` | Optional metric minimized for `best.ema.ckpt`; default `validation_loss` |

`keep_every_steps` must be a multiple of `save_every_steps`. When validation
is enabled, `validate_every_steps` must also be a multiple of
`save_every_steps` so the selected state is resumable.

## Released recipes

| Recipe | Reads by default | Writes |
|---|---|---|
| `audio2sph.yaml` | Prepared VCTK through Online Scene Generation | `runs/audio2sph_pretraining/` |
| `sourcebank_passt.yaml` | `audio2sph.ckpt`, PaSST tensors from `said_passt.ckpt`, and SourceBank | `runs/said_sourcebank_passt/` |
| `sourcebank_audiomae.yaml` | `audio2sph.ckpt`, AudioMAE tensors from `said_audiomae.ckpt`, and SourceBank | `runs/said_sourcebank_audiomae/` |
| `dcase_passt.yaml` | `said_passt.ckpt` and DCASE recordings | `runs/said_dcase_fine_tuning_passt/` |
| `dcase_audiomae.yaml` | `said_audiomae.ckpt` and DCASE recordings | `runs/said_dcase_fine_tuning_audiomae/` |

These defaults make each recipe an independent published-model entry point.
To carry newly trained weights forward, replace the next recipe's
`load_audio2sph_ckpt` or `load_said_ckpt` with the preceding `.ema.ckpt`.
[Training](training.md#pass-a-newly-trained-model-to-the-next-route) gives the
exact handoff paths.

## Checkpoint paths and resume

Canonical published paths in the repository-root `checkpoints/` directory are
downloaded from the official
[Hugging Face repository](https://huggingface.co/IN03X/SAID) and verified by
byte length and SHA256. `SAID_CHECKPOINT_BASE_URL` selects another authorized
mirror. Other checkpoint paths must already exist locally.

`load_said_ckpt` and `load_audio2sph_ckpt` accept flat model-weight files for
initialization. Rerunning the same training command instead restores
`latest.training.pt` from `output_directory`. `--resume PATH` selects another
complete training state, and the stored training-recipe and data-configuration
fingerprint must match the current files.

## Data configuration

`configs/data.yaml` contains these sections:

- `dcase_recordings`: DCASE root, split names, rotation views, source limit,
  and class balancing;
- `simulated_scenes`: VCTK, SourceBank, room, source, activity, noise, and
  target-map settings;
- `audio`: microphone array, **1-based Eigenmike capsule numbers**
  `[6, 10, 26, 22]`, 48 kHz sample rate, and two-second segment duration.

Only the section selected by the recipe's `data` field is validated and used.
[Data](data.md) gives the required directory layouts and SourceBank manifest
schema.
