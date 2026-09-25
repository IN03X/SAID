# SAID (AudioMAE) checkpoint card

## Intended use

This checkpoint reproduces the SAID (AudioMAE) model reported in the paper. It
predicts separate 180×360 labeled acoustic maps from four Eigenmike capsule
signals at 10 fps.

## Identity

- Canonical filename: `said_audiomae.ckpt`
- Format: flat exponential-moving-average PyTorch state dictionary
- Size: 549,046,019 bytes
- SHA256: `018f9d05bfa681456334811abe6e62c59331a0a8f4313eb763e5c32b0148c626`
- Runtime tensors: 732
- Submitted tensors covered by the fingerprint: 750

## Input and output contract

The model accepts the **1-based Eigenmike capsule numbers** `[6, 10, 26, 22]`
at 48 kHz in two-second windows and predicts 16 candidate slots over the fixed
13-class taxonomy. It produces acoustic maps and class/confidence values; it
does not predict source distance. The common recording-domain argument is
accepted for API consistency and does not apply a domain embedding in this
variant.

The open-source runtime includes physical-time alignment for AudioMAE tokens.
The paper-checkpoint loader accepts the fingerprinted complete SAID checkpoint;
it is not a converter for an upstream AudioMAE backbone.

## Reported development-test metrics

Here (4.2) means 2026-04-02, commit `84b2cd1`; (6.30) means 2026-06-30,
commit `d4df662`. See [Evaluation](../evaluation.md#metric-versions).

| Macro mAP (4.2) | Mask AP (4.2) | Macro mAP (6.30) | Class-agnostic AP (6.30) | Macro Pearson r (6.30) | Macro Class-F1 |
|---:|---:|---:|---:|---:|---:|
| 0.113441 | 0.228684 | 0.124763 | 0.193225 | 0.430736 | 0.395145 |

The metrics use all 78 full development-test recordings. Pearson correlation
uses the official macro protocol.

## Training provenance

The checkpoint contains the complete AudioMAE Class Feature Encoder initialized
from:

- Upstream model ID: `gaunernst/vit_base_patch16_1024_128.audiomae_as2m_ft_as20k`
- Upstream revision: `49cfdfa4663a9c689e7ee5184df2d5660f730233`
- Port and asset publisher: `gaunernst`
- Port source: <https://huggingface.co/gaunernst/vit_base_patch16_1024_128.audiomae_as2m_ft_as20k>
- Original work: *Masked Autoencoders that Listen*, Po-Yao Huang, Hu Xu,
  Juncheng Li, Alexei Baevski, Michael Auli, Wojciech Galuba, Florian Metze,
  and Christoph Feichtenhofer
- Original source: <https://github.com/facebookresearch/AudioMAE>
- Applied upstream license: [Creative Commons Attribution-NonCommercial 4.0](https://creativecommons.org/licenses/by-nc/4.0/legalcode.en)

The backbone was integrated as the Class Feature Encoder and trained within
complete SAID on SourceBank and DCASE recordings. The internal 122,359-row
SourceBank index has SHA256
`489f2405025d64f087a76a753cb0fae1051e91df00236ae2285ca2046d664535`.
The index and source audio are not distributed. Dataset composition, selected
source counts, and attribution are summarized in the
[training data notice](../../LICENSES/TRAINING_DATA.md).

## Distribution and license

The selected upstream Hugging Face metadata names CC-BY-4.0, while the same
revision's configuration and the original AudioMAE repository license indicate
CC BY-NC 4.0. This release applies the stricter CC BY-NC 4.0 status to the
incorporated AudioMAE asset. The complete checkpoint is distributed for
non-commercial research under the
[SAID Model Weights Non-Commercial Research License 1.0](../../LICENSES/SAID-Model-Weights-NonCommercial-1.0.txt),
subject to all applicable upstream and training-data terms. That license grants
only rights held by Runbang Wang and does not replace or enlarge third-party
permissions. The checkpoint is distributed separately from the source package
and runtime wheel.

Contains information from the CSTR VCTK Corpus, made available under the ODC
Attribution License 1.0. The corpus is copyright 2012, Centre for Speech
Technology Research, University of Edinburgh, and was constructed by
Christophe Veaux, Junichi Yamagishi, and Kirsten MacDonald. The official VCTK
0.80 record is <https://datashare.ed.ac.uk/handle/10283/2651>.

Exact third-party revisions and attribution are recorded in
[Third-party notices](../../THIRD_PARTY_NOTICES.md).
