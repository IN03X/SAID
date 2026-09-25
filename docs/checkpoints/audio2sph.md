# Audio2Sph + Panoramic Decoder checkpoint card

## Intended use

This checkpoint is the paper's class-agnostic acoustic-imaging model. It maps
four selected Eigenmike capsule signals to one 180×360 acoustic field at the
native 100 Hz time base. It contains Audio2Sph and the Panoramic Decoder; it
does not contain Sph2Imaging, a Class Feature Encoder, or class predictions.

## Identity

- Canonical filename: `audio2sph.ckpt`
- Format: flat exponential-moving-average PyTorch state dictionary
- Size: 110,233,837 bytes
- SHA256: `bd4c2c44e24d4c207785a46cfc95efffc3f9a7a9a357d4563553a886be0b917b`
- Tensors: 245 total; 233 Audio2Sph and 12 Panoramic Decoder
- Input representation: STFT log magnitude, sine phase, and cosine phase

The public loader verifies the size, SHA256, component counts, tensor keys,
shapes, and dtypes before strict loading.

## Input and output contract

The model accepts the **1-based Eigenmike capsule numbers** `[6, 10, 26, 22]`
at 48 kHz in two-second windows. The Panoramic Decoder produces 201
endpoint-inclusive maps per window. Complete-recording inference omits each
window endpoint before concatenation, yielding continuous 100 Hz frame starts.
Output values are sigmoid probabilities in `[0,1]` and use the same spherical
coordinates as complete SAID maps.

## Training provenance

The checkpoint is the final EMA weight of the paper's Audio2Sph pretraining
route after approximately 2.5 million optimizer updates with the published
magnitude-and-phase input representation. Training used two-second, 48 kHz
four-channel Eigenmike scenes rendered online from VCTK 0.80 speech with
shoebox image-source simulation. The target at every time position is the
pixelwise maximum of all active source maps.

## Distribution and license

The checkpoint is distributed for non-commercial research under the
[SAID Model Weights Non-Commercial Research License 1.0](../../LICENSES/SAID-Model-Weights-NonCommercial-1.0.txt)
for rights held by Runbang Wang, together with applicable upstream terms. It
contains information from the CSTR VCTK Corpus 0.80, made available under the
Open Data Commons Attribution License 1.0. The corpus is copyright 2012,
Centre for Speech Technology Research, University of Edinburgh, and was
constructed by Christophe Veaux, Junichi Yamagishi, and Kirsten MacDonald.
Training recordings are not distributed with the checkpoint.

ConvNeXt and NESD-derived software attribution and exact source revisions are
recorded in [Third-party notices](../../THIRD_PARTY_NOTICES.md). The training
data terms are summarized in the
[training data notice](../../LICENSES/TRAINING_DATA.md).
