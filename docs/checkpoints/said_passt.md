# SAID (PaSST) checkpoint card

## Intended use

This checkpoint reproduces the SAID (PaSST) model reported in the paper. It
predicts separate 180×360 labeled acoustic maps from four Eigenmike capsule
signals at 10 fps.

## Identity

- Canonical filename: `said_passt.ckpt`
- Format: flat exponential-moving-average PyTorch state dictionary
- Size: 547,886,573 bytes
- SHA256: `85b72bd4a0749dd4ca080ddea30eba68a903af465d916408a03e21912609ca1b`
- Runtime tensors: 741
- Submitted tensors covered by the fingerprint: 757

## Input and output contract

The model accepts the **1-based Eigenmike capsule numbers** `[6, 10, 26, 22]`
at 48 kHz in two-second windows and predicts 16 candidate slots over the fixed
13-class taxonomy. It produces acoustic maps and class/confidence values; it
does not predict source distance.

The checkpoint contains Sony and TAU recording-domain embeddings. Sony is the
generic inference default and is not automatic domain detection. DCASE
evaluation selects the embedding from the dataset subset.

## Reported development-test metrics

Here (4.2) means 2026-04-02, commit `84b2cd1`; (6.30) means 2026-06-30,
commit `d4df662`. See [Evaluation](../evaluation.md#metric-versions).

| Macro mAP (4.2) | Mask AP (4.2) | Macro mAP (6.30) | Class-agnostic AP (6.30) | Macro Pearson r (6.30) | Macro Class-F1 |
|---:|---:|---:|---:|---:|---:|
| 0.120150 | 0.237972 | 0.124514 | 0.197483 | 0.426790 | 0.388488 |

The metrics use all 78 full development-test recordings. Pearson correlation
uses the official macro protocol.

## Training provenance

### PaSST initialization

The Class Feature Encoder descends from the official AudioSet-pretrained
PaSST-S checkpoint:

- Upstream repository: <https://github.com/kkoutini/PaSST>
- Upstream revision: `2a5c818afcc2a215b2a1aaf1ed8be71f89d43201`
- Architecture identifier: `passt_s_swa_p16_128_ap476`
- Initial-weight asset: `passt-s-f128-p16-s10-ap.476-swa.pt`
- Initial-weight URL: <https://github.com/kkoutini/PaSST/releases/download/v0.0.1-audioset/passt-s-f128-p16-s10-ap.476-swa.pt>
- Initial-weight SHA256: `302903fa8c4aee817b11dc982da0b29aaf8d11a3e722420476d0a12c9db70c2c`
- PaSST software license: Apache-2.0; see the
  [included license](../../LICENSES/PaSST-Apache-2.0.txt)

The PaSST encoder was adapted to produce frequency-pooled temporal Class
Features, further trained for DCASE sound recognition, and frozen while the
complete SAID detector was trained.

### SAID training data

- Audio2Sph pretraining used online simulated scenes containing VCTK sources.
- Complete SAID training used SourceBank indices over VCTK, MUSDB18-HQ,
  FSD50K, and verified FSDKaggle2018 clips, rendered into online scenes.
- Final fine-tuning used the DCASE2026 Task 3 Track A development-training
  recordings and labels.

The internal SourceBank index contained 122,359 rows and has SHA256
`489f2405025d64f087a76a753cb0fae1051e91df00236ae2285ca2046d664535`.
It is retained as provenance and is not distributed. Audio files and dataset
labels are not included with the checkpoint. The complete data provenance is
summarized in the [training data notice](../../LICENSES/TRAINING_DATA.md).

## Distribution and license

The checkpoint is distributed for non-commercial research under the
[SAID Model Weights Non-Commercial Research License 1.0](../../LICENSES/SAID-Model-Weights-NonCommercial-1.0.txt),
subject to every applicable upstream term. It contains a PaSST-derived
backbone and parameters trained from the SourceBank mixture described above.
The SAID weight license applies only to rights held by Runbang Wang and does
not replace upstream terms. Original training audio and labels are not
included.

The PaSST project distributes its software under Apache-2.0 and presents the
initial-weight asset above as an official pretrained model for inference and
fine-tuning. The SourceBank mixture includes non-commercial, restricted, or
Sampling+ terms; this release therefore applies the non-commercial research
boundary to the complete checkpoint. Exact revisions and attribution are
recorded in [Third-party notices](../../THIRD_PARTY_NOTICES.md).

Contains information from the CSTR VCTK Corpus, made available under the ODC
Attribution License 1.0. The corpus is copyright 2012, Centre for Speech
Technology Research, University of Edinburgh, and was constructed by
Christophe Veaux, Junichi Yamagishi, and Kirsten MacDonald. The official VCTK
0.80 record is <https://datashare.ed.ac.uk/handle/10283/2651>.
