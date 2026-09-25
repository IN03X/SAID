# Model and asset license status

The software license in [`LICENSE`](../LICENSE) applies to original SAID
software and documentation. Model weights, datasets, third-party
implementations, and media assets retain their own distribution terms.

This directory uses the conventional `LICENSES/` name. Its `.txt` files are
the applicable license texts, while [`THIRD_PARTY_NOTICES.md`](../THIRD_PARTY_NOTICES.md)
maps each external component to its source, revision, use in SAID, and license.

| Artifact | Distribution status | Terms and required action |
|---|---|---|
| SAID (PaSST) checkpoint | Separate non-commercial research release | Governed by the SAID Model Weights Non-Commercial Research License 1.0 for rights held by Runbang Wang, together with all applicable upstream terms and the PaSST checkpoint card |
| SAID (AudioMAE) checkpoint | Separate non-commercial research release | Governed by the SAID Model Weights Non-Commercial Research License 1.0 for rights held by Runbang Wang; the stricter CC BY-NC 4.0 status and attribution notice are applied to the incorporated AudioMAE asset |
| Audio2Sph + Panoramic Decoder checkpoint | Separate non-commercial research release | Governed by the SAID Model Weights Non-Commercial Research License 1.0 for rights held by Runbang Wang, together with the VCTK produced-work notice and applicable ConvNeXt/NESD terms |
| DCASE data and labels | Not distributed | STAIRS26 and STARSS23 official deposits declare MIT for the cited versions; users obtain the data from the official distribution and retain its notices |
| CSTR VCTK Corpus 0.80 | Not distributed; used to train the paper checkpoints | Contains information from the CSTR VCTK Corpus, made available under the ODC Attribution License 1.0, <http://opendatacommons.org/licenses/by/1.0/>; official corpus record: <https://datashare.ed.ac.uk/handle/10283/2651> |
| MUSDB18-HQ, FSD50K, and FSDKaggle2018 | Not distributed; used to train the paper checkpoints | Source terms and selected-source counts are provided in [`TRAINING_DATA.md`](TRAINING_DATA.md); the complete attribution record accompanies each checkpoint bundle |
| Demonstration media | Included | Four 20-second STAIRS26 four-channel audio/label excerpts and synchronized STARSS23 video excerpts, plus the generated SAID (PaSST) comparisons for Demo 1 and Demo 3 in the repository README; copyright Sony and Tampere University, MIT |

The SAID (AudioMAE) architecture and runtime remain available. The public
checkpoint loader accepts the fingerprinted complete paper checkpoint; it is
not a converter for an upstream AudioMAE backbone. The two complete paper
checkpoints are distributed separately from the source distribution and
runtime wheel. They are accompanied by their checkpoint cards,
[`THIRD_PARTY_NOTICES.md`](../THIRD_PARTY_NOTICES.md), and
[`SAID-Model-Weights-NonCommercial-1.0.txt`](SAID-Model-Weights-NonCommercial-1.0.txt).

The non-commercial license grants only the rights held by Runbang Wang. It does
not replace or enlarge any third-party permission. Original training data are
not included with the checkpoints.

The MIT-licensed SAID software may be used independently of these paper
checkpoints. Commercial use of any published checkpoint, including a
modified or fine-tuned derivative, is prohibited. A user may instead train a
new model with the SAID software and independently obtained components and
training data that permit the intended commercial use. The permissions for
that independently trained model follow the user's selected assets and data;
the paper-checkpoint license does not apply to weights that do not derive from
the paper checkpoints.
