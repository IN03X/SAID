# Third-party notices

SAID contains original software by Runbang Wang and the adapted components
listed below. Pretrained weights, datasets, and demonstration media have
separate distribution terms recorded in `LICENSES/README.md`.

## ConvNeXt

- Source: https://github.com/facebookresearch/ConvNeXt
- Revision: `048efcea897d999aed302f2639b6270aedf8d4c8`
- Pinned source: https://github.com/facebookresearch/ConvNeXt/tree/048efcea897d999aed302f2639b6270aedf8d4c8
- Copyright: Meta Platforms, Inc. and affiliates
- License: MIT; see `LICENSES/ConvNeXt-MIT.txt`
- Use in SAID: ConvNeXt block and channel-first/channel-last normalization in
  `said/models/audio2sph.py`, adapted and integrated by Runbang Wang.

## Deformable DETR

- Source: https://github.com/fundamentalvision/Deformable-DETR
- Revision: `11169a60c33333af00a4849f1808023eba96a931`
- Pinned source: https://github.com/fundamentalvision/Deformable-DETR/tree/11169a60c33333af00a4849f1808023eba96a931
- Copyright: 2020 SenseTime
- License: Apache-2.0; see
  `LICENSES/Deformable-DETR-Apache-2.0.txt`
- Use in SAID: multi-scale deformable attention sampling, initialization, and
  encoder structure in `said/models/sph2imaging_blocks/multi_scale_features.py`, adapted and
  modified by Runbang Wang.

## Mask2Former

- Source: https://github.com/facebookresearch/Mask2Former
- Revision: `9b0651c6c1d5b3af2e6da0589b719c514ec0d69a`
- Pinned source: https://github.com/facebookresearch/Mask2Former/tree/9b0651c6c1d5b3af2e6da0589b719c514ec0d69a
- Copyright: 2022 Meta, Inc.; source files also retain
  `Copyright (c) Facebook, Inc. and its affiliates.`
- License: MIT; see `LICENSES/Mask2Former-MIT.txt`
- Use in SAID: positional encoding, multi-scale pixel-decoder organization,
  Mask Decoder attention layers, pairwise BCE/Dice costs, Hungarian matching,
  and criterion organization in `said/models/sph2imaging_blocks/multi_scale_features.py`,
  `said/models/sph2imaging_blocks/mask_decoder.py`, `said/training/matcher.py`, and
  `said/training/losses.py`, adapted and modified by Runbang Wang.

## Detectron2 PointRend

- Source: https://github.com/facebookresearch/detectron2
- Revision: `9604f5995cc628619f0e4fd913453b4d7d61db3f`
- Pinned source: https://github.com/facebookresearch/detectron2/tree/9604f5995cc628619f0e4fd913453b4d7d61db3f
- Copyright: Facebook, Inc. and its affiliates
- License: Apache-2.0; see `LICENSES/Detectron2-Apache-2.0.txt`.
- Use in SAID: normalized bilinear point sampling and uncertainty-guided point
  selection in `said/training/matcher.py` and
  `said/training/losses.py`, adapted and modified by Runbang Wang.

## SAM 2

- Source: https://github.com/facebookresearch/sam2
- Revision: `2b90b9f5ceec907a1c18123530e92e794ad901a4`
- Pinned source: https://github.com/facebookresearch/sam2/tree/2b90b9f5ceec907a1c18123530e92e794ad901a4
- Copyright: Meta Platforms, Inc. and affiliates
- License: Apache-2.0; see `LICENSES/SAM2-Apache-2.0.txt`
- Use in SAID: transposed-convolution upsampling, two-dimensional layer
  normalization, query-conditioned map embeddings, and feature/embedding dot
  products in `said/models/sph2imaging_blocks/refine_block.py`, adapted and modified by
  Runbang Wang.

## PaSST

- Source: https://github.com/kkoutini/PaSST
- Revision: `2a5c818afcc2a215b2a1aaf1ed8be71f89d43201`
- Pinned source: https://github.com/kkoutini/PaSST/tree/2a5c818afcc2a215b2a1aaf1ed8be71f89d43201
- License: Apache-2.0; see `LICENSES/PaSST-Apache-2.0.txt`
- Use in SAID: the patch embedding, time/frequency positional embedding,
  transformer-block, and frequency-pooling feature-extraction flow in
  `said/models/class_feature_encoders/passt.py`. The file is modified to produce
  frame-aligned Class Features and apply the recording-domain embedding stored
  in the paper checkpoint.

## AudioMAE model asset

- Port source: https://huggingface.co/gaunernst/vit_base_patch16_1024_128.audiomae_as2m_ft_as20k
- Port revision: `49cfdfa4663a9c689e7ee5184df2d5660f730233`
- Port publisher: `gaunernst`
- Original source: https://github.com/facebookresearch/AudioMAE
- Original work: *Masked Autoencoders that Listen*, by Po-Yao Huang, Hu Xu,
  Juncheng Li, Alexei Baevski, Michael Auli, Wojciech Galuba, Florian Metze,
  and Christoph Feichtenhofer
- Applied license: Creative Commons Attribution-NonCommercial 4.0
  International, https://creativecommons.org/licenses/by-nc/4.0/legalcode.en
- License text: `LICENSES/AudioMAE-CC-BY-NC-4.0.txt`
- License-status note: the Hugging Face model card declares CC BY 4.0, while
  the original AudioMAE repository and associated configuration identify CC
  BY-NC 4.0. SAID applies the stricter CC BY-NC 4.0 status.
- Modifications: the timm-compatible AudioMAE backbone was integrated as the
  SAID Class Feature Encoder, trained within complete SAID on SourceBank and
  DCASE recordings, and packaged with SAID parameters by Runbang Wang.
- Distribution boundary: this model asset is not part of the runtime wheel.
  The complete SAID (AudioMAE) checkpoint is a separate
  non-commercial research release governed by its checkpoint card, the SAID
  weight license for rights held by Runbang Wang, and the applicable upstream
  terms.

## NESD release_v1.0 rendering components

- Source: https://github.com/qiuqiangkong/nesd/tree/release_v1.0
- Revision: `7475da725dcfc8d60761c5891644ca326a7226e7`
- Pinned source: https://github.com/qiuqiangkong/nesd/tree/7475da725dcfc8d60761c5891644ca326a7226e7
- Copyright: 2025 CUHK
- License: MIT; see `LICENSES/NESD-MIT.txt`
- Use in SAID: Eigenmike geometry, rigid-sphere response construction,
  shoebox image-source geometry, and directional acoustic-rendering formulas
  in `said/rendering/` and `said/arrays/eigenmike32.csv`, adapted and
  modified by Runbang Wang.
- Contributor boundary: the upstream rigid-sphere implementation identifies
  Yin Cao and Qiuqiang Kong as its authors. The upstream shoebox ISM and
  renderer identify Qiuqiang Kong/CUHK; Yin Cao is not attributed to those
  separate components.

## DCASE2026 Task 3 evaluator

- Source: https://github.com/iranroman/DCASE2026_Task3_SAISELD_baseline
- Download sources:
  - Current official evaluator: https://raw.githubusercontent.com/iranroman/DCASE2026_Task3_SAISELD_baseline/main/evaluate.py
  - Optional paper COCO AP revision: https://raw.githubusercontent.com/iranroman/DCASE2026_Task3_SAISELD_baseline/84b2cd1/evaluate.py
- Use in SAID: `said evaluate` downloads the current official evaluator from
  its original repository. `--add-previous-metrics` additionally downloads commit
  `84b2cd1`, which supplies the paper's class-aware COCO Macro mAP and
  class-agnostic Mask AP. The downloaded-file SHA256 is recorded with each
  result for provenance but is not used as a version gate.
- Distribution boundary: no evaluator source from this repository is committed
  to SAID or included in its wheel or source distribution. The downloaded file
  remains under its upstream terms. The repository did not include a license
  file when audited, so SAID does not redistribute or relicense it.
- SAID diagnostics: class-agnostic AP is evaluated by merging every category.
  The optional paper Mask AP uses one explicit `All classes` COCO category. The
  20-degree matched Class Macro-F1 implementation in
  `said/evaluation/metrics/class_f1.py` is original SAID software released
  under MIT.

## CSTR VCTK Corpus 0.80

- Use in SAID: source recordings used by Online Scene Generation for
  Audio2Sph pretraining and in the paper checkpoint's SourceBank training.
- Corpus copyright: 2012, Centre for Speech Technology Research, University of
  Edinburgh.
- Corpus construction: Christophe Veaux, Junichi Yamagishi, and Kirsten
  MacDonald.
- License: Open Data Commons Attribution License 1.0,
  <http://opendatacommons.org/licenses/by/1.0/>.
- Official corpus record: CSTR VCTK Corpus 0.80,
  <https://datashare.ed.ac.uk/handle/10283/2651>.
- Produced-work notice: Contains information from the CSTR VCTK Corpus, which
  is made available under the ODC Attribution License.
- Distribution boundary: SAID does not distribute VCTK recordings. Readers
  obtain the corpus from its rightsholder and retain its `README` and
  `COPYING` notices.

## STAIRS26 and STARSS23 demonstration media

- STAIRS26 source: https://doi.org/10.5281/zenodo.18171005
- STARSS23 source: https://doi.org/10.5281/zenodo.7880637
- Copyright: 2026 SONY and Tampere University (STAIRS26); 2023 SONY and
  Tampere University (STARSS23)
- License: MIT; see `LICENSES/STAIRS26-MIT.txt` and
  `LICENSES/STARSS23-MIT.txt`
- Use in SAID: the release includes four fixed 20-second excerpts: seconds
  150--170 of
  `dev-test-tau/fold4_room8_mix003`, seconds 50--70 of
  `dev-test-tau/fold4_room10_mix007`, seconds 20--40 of
  `dev-test-sony/fold4_room24_mix002`, and seconds 0--20 of
  `dev-test-sony/fold4_room23_mix003`. Four Eigenmike capsules selected from
  STAIRS26 audio and its acoustic-map labels are synchronized with the
  corresponding STARSS23 panoramas. Every generated comparison places Ground
  Truth and SAID Prediction side by side with a fixed class legend. The
  repository README includes the generated SAID (PaSST) comparisons for Demo 1
  and Demo 3; the source and wheel include all four excerpt inputs for local
  generation. Complete recordings remain with the official dataset releases.

## Published-checkpoint training sources

The complete paper checkpoints were trained with locally obtained VCTK 0.80,
MUSDB18-HQ, FSD50K, FSDKaggle2018, STAIRS26, and STARSS23 materials. Apart from
the four documented demo excerpts, no recording, training collection,
SourceBank audio, or dataset archive is included in this release.
Dataset-level notices, selected-source license counts, and source attribution
records are provided in `LICENSES/TRAINING_DATA.md` and in each separately
distributed checkpoint bundle.

The class-agnostic Audio2Sph + Panoramic Decoder checkpoint used only VCTK
0.80 sources and Online Scene Generation. It contains no Class Feature
Encoder, SourceBank class labels, or DCASE fine-tuning parameters.

The published checkpoints are distributed for non-commercial research
only. This restriction also applies to modified or fine-tuned weights derived
from them. Independent models trained with the SAID software are governed by
the components and data selected for those independent runs.

## Pyroomacoustics

- Source: https://github.com/LCAV/pyroomacoustics
- Version: `0.10.1`
- Copyright: EPFL-LCAV
- License: MIT
- Use in SAID: optional dependency that computes shoebox image-source
  positions and reflection orders for Online Scene Generation. It is not
  vendored in this repository.

Names such as AudioMAE, DCASE, and Eigenmike identify compatible
external components and protocols. Their names do not change the licensing
boundary recorded here and in `LICENSES/README.md`.

Direct Python dependencies are installed as separate distributions rather than
vendored into this repository. Their declared constraints are listed in
`pyproject.toml`, and each installed distribution retains its upstream license.
