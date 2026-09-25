# Data

SAID uses reader-provided datasets through three explicit interfaces. Dataset
files remain under the terms published by their respective rights holders.
The training command validates the selected interface before constructing a
model or optimizer.

## Training data at a glance

All local paths are configured once in `configs/data.yaml`. Absolute paths are
accepted; relative paths are resolved from the directory containing that file.

| Training recipe | Local data | Fields to set | Preparation or training command |
|---|---|---|---|
| DCASE fine-tuning | Extracted DCASE2026 Task 3 development audio and labels | `dcase_recordings.root` | `said train --config configs/training/dcase_passt.yaml` |
| Audio2Sph pretraining | Original VCTK v0.80 corpus and an empty output directory | `simulated_scenes.vctk.source_root`, `simulated_scenes.vctk.prepared_root` | `said prepare --config configs/training/audio2sph.yaml` |
| Complete SAID SourceBank training | Local class-labeled clips and a SourceBank manifest | `simulated_scenes.sourcebank.manifest`, `simulated_scenes.sourcebank.source_audio_root` | `said train --config configs/training/sourcebank_passt.yaml` |

The AudioMAE recipes replace `passt` with `audiomae`. Each command validates
only the local files used by its selected data adapter, so DCASE fine-tuning
does not require VCTK or SourceBank files.

## DCASE2026 recordings

Obtain the DCASE2026 Task 3 Track A development data from the official
distribution and preserve its directory names. Evaluation and DCASE
fine-tuning use these two archives from the STAIRS26 record:

- [`32ch_audio_dev.zip`](https://zenodo.org/api/records/18171005/files/32ch_audio_dev.zip/content);
- [`labels_dev.zip`](https://zenodo.org/api/records/18171005/files/labels_dev.zip/content).

The official task and dataset records are:

- DCASE2026 Task 3: <https://dcase.community/challenge2026/>
- STAIRS26, DOI
  [10.5281/zenodo.18171005](https://doi.org/10.5281/zenodo.18171005),
  by the Sony and Tampere University contributors named in the record;
- STARSS23, DOI
  [10.5281/zenodo.7880637](https://doi.org/10.5281/zenodo.7880637),
  by the Sony and Tampere University contributors named in the record.

STAIRS26 contains the two packaged development archives consumed by SAID;
STARSS23 is retained here as an upstream data citation. The cited Zenodo
records declare the MIT License for their corresponding deposited versions.
Users retain the notices supplied with the exact files they download. The
dataset is not bundled with SAID.

```text
$HOME/datasets/dcase2026_task3/
├── eigen_dev/
│   ├── dev-train-sony/*.wav
│   ├── dev-train-tau/*.wav
│   ├── dev-test-sony/*.wav
│   └── dev-test-tau/*.wav
└── labels_dev/
    ├── dev-train-sony/*_std.json
    ├── dev-train-tau/*_std.json
    ├── dev-test-sony/*_std.json
    └── dev-test-tau/*_std.json
```

For evaluation, pass `$HOME/datasets/dcase2026_task3` directly to
`said evaluate`. For training, set the same directory as
`dcase_recordings.root` in `configs/data.yaml`. The adapter verifies
paired recording and label stems, 32-channel Eigenmike input, class indices,
and official 10 Hz frame indices. It selects capsules 6, 10, 26, and 22 and
constructs the four azimuth-rotation views described in the paper.

## VCTK for Audio2Sph pretraining

Install the Online Scene Generation dependencies before preparing data or
starting simulated-scene training:

```bash
pip install -e '.[render]'
```

The paper uses VCTK release 0.80 with 109 speakers. Obtain this release from the
[official University of Edinburgh corpus record](https://datashare.ed.ac.uk/handle/10283/2651).
Its accompanying database license is ODC Attribution License 1.0. Retain the
downloaded `README` and `COPYING` files, then place the corpus at the path
selected by `simulated_scenes.vctk.source_root`:

```text
/path/to/vctk-v0.80/
├── README
├── COPYING
└── wav48/
    └── p*/**.wav
```

Set `simulated_scenes.vctk.prepared_root` to an empty output directory and run:

```bash
said prepare --config configs/training/audio2sph.yaml
```

The preparation command checks the release identity in `README`, the ODC-By
1.0 notice in `COPYING`, and all 109 speaker directories. It divides the sorted
speaker list into 99 training speakers and 10 held-out speakers. Speech activity
is measured by RMS over 0.05 s windows with a 0.1 hop ratio; samples covered by
windows at or below 0.0055 RMS are removed. Each remaining waveform is repeated
or truncated to exactly two seconds at 48 kHz. The prepared directory retains
the VCTK notices and a machine-readable record of these parameters. Audio2Sph
training validates that record before Online Scene Generation begins.

## SourceBank for complete SAID training

SourceBank is a local, license-aware index over clips obtained by each reader.
Its public interface consists of a TSV or CSV manifest and an audio root.
One practical layout is:

```text
/path/to/sourcebank/
├── audio/
│   ├── female_speech_001.wav
│   ├── male_speech_001.wav
│   └── ...
└── sourcebank.csv
```

The paper recipe requires at least one authorized row for every class ID from
0 through 12. The fixed mapping is:

| ID | Class | ID | Class |
|---:|---|---:|---|
| 0 | Female speech | 7 | Door open/close |
| 1 | Male speech | 8 | Music |
| 2 | Clapping | 9 | Musical instr. |
| 3 | Telephone | 10 | Water tap/shower |
| 4 | Laughter | 11 | Bell |
| 5 | Domestic sounds | 12 | Knock |
| 6 | Walk/footsteps |  |  |

The manifest fields are:

| Field | Contract |
|---|---|
| `source_dataset` | Dataset or collection name |
| `source_id` | Unique stable identifier |
| `local_audio_path` | Path relative to `source_audio_root` |
| `class_id` | Zero-based DCASE class in `[0,12]` |
| `start_seconds`, `end_seconds` | Authorized segment with `0 <= start < end` |
| `license_spdx_or_uri` | SPDX identifier or stable license URI |
| `attribution` | Attribution required by the source license |
| `source_page` | Stable source or dataset page |
| `redistribution_allowed` | Explicit `true` or `false` |
| `commercial_use_allowed` | Explicit `true` or `false` |
| `training_use_allowed` | Explicit `true` or `false` |

A minimal CSV row has the following form; a `.tsv` file uses the same columns
with tab separators:

```csv
source_dataset,source_id,local_audio_path,class_id,start_seconds,end_seconds,license_spdx_or_uri,attribution,source_page,redistribution_allowed,commercial_use_allowed,training_use_allowed
local_collection,female-speech-001,female_speech_001.wav,0,0.0,2.0,LicenseRef-UserVerified,Creator or collection attribution,https://example.org/source,false,false,true
```

`source_id` values are unique. `local_audio_path` is relative to
`source_audio_root`. Users keep every time range inside its referenced clip;
the manifest validator checks numeric ordering, and selected material shorter
than two seconds is padded to the scene length. Boolean permission values are
written literally as `true` or `false`.

Permission fields use a default-deny policy. Every row must explicitly
authorize training, and `require_commercial_use: true` additionally requires
commercial-use authorization. Absolute paths and parent-directory traversal
are rejected. The manifest therefore records provenance and permission while
the audio remains in the reader's licensed local collection.

The validator enforces the declarations supplied in the manifest; it does not
determine the legal accuracy of those declarations. Users verify the source
terms and their intended use before setting the permission fields.
`require_commercial_use: false` permits a manifest to include
non-commercially licensed material. A model trained from such a manifest has
its own distribution review and does not acquire the software's MIT license.
For an independently trained commercial model, set
`require_commercial_use: true` and use a Class Feature Encoder and
initialization that also permit the intended commercial use. The official
paper checkpoints cannot be used as initialization for that route.

The paper checkpoint used an internal 122,359-row SourceBank index with SHA256
`489f2405025d64f087a76a753cb0fae1051e91df00236ae2285ca2046d664535`.
Its composition was 43,832 VCTK rows, 70,416 MUSDB18-HQ rows, 4,672 FSD50K
rows, and 3,439 verified FSDKaggle2018 rows. That index is retained as
provenance and is not distributed. It predates the public per-row permission
fields, so the public schema is a rights-aware reconstruction interface rather
than a byte-identical representation of the internal manifest.
The paper checkpoint's selected-source attribution record accompanies each
separately distributed checkpoint bundle and is summarized in
[`LICENSES/TRAINING_DATA.md`](../LICENSES/TRAINING_DATA.md).

Set these fields in `configs/data.yaml`:

```yaml
simulated_scenes:
  sourcebank:
    manifest: /path/to/sourcebank/sourcebank.csv
    source_audio_root: /path/to/sourcebank/audio
    require_commercial_use: false
```

SAID validates every manifest row before training. Online Scene Generation
then samples 1--6 labeled sources, room geometry, source regions, activity,
and additive noise according to the paper configuration.

## Data configuration

`configs/data.yaml` contains both recorded-data and simulated-scene
sections. The top-level training configuration selects one with
`data: dcase_recordings` or `data: simulated_scenes`. Paths may be absolute or
relative to the data configuration file. `audio.array: eigenmike32` selects
the Eigenmike geometry, while `capsule_indices_1based: [6, 10, 26, 22]`
records the four **1-based Eigenmike capsule numbers** used by SAID.
