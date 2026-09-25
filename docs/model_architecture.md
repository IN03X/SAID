# Model Architecture

The public model hierarchy follows the paper:

```text
SAID
├── Audio2Sph
│   ├── STFT magnitude and phase
│   ├── ConvNeXt features X1, X2, X3
│   ├── Spherical Cross-Attention → Y1, Y2, Y3
│   ├── Y0 = Y1 + Y2 + Y3
│   └── Time Alignment → Panoramic Features
└── Sph2Imaging
    ├── Multi-Scale Deformable Attention
    │   └── Small, Medium, and Large Features
    ├── Up Blocks → Map Features
    ├── Class Feature Encoder (PaSST or AudioMAE)
    ├── nine-layer Mask Decoder
    │   └── Masked Cross-Attention
    │       → Class Feature Cross-Attention
    │       → Self-Attention → FFN
    ├── Active Head
    ├── Map Head
    ├── Class Head
    └── Refine Block
```

The Mask Decoder reads Large, Medium, and Small Features in that order and
repeats the cycle three times. It produces predictions before the first layer
and after each of the nine layers. Class Feature Cross-Attention remains active
in all nine layers.

## Paper-to-code names

| Paper component | Python class | Source file |
|---|---|---|
| SAID | `SAID` | `said/models/said.py` |
| Audio2Sph | `Audio2Sph` | `said/models/audio2sph.py` |
| Spherical Cross-Attention | `SphericalCrossAttention` | `said/models/audio2sph.py` |
| Time Alignment | `TimeAlignment` | `said/models/audio2sph.py` |
| Panoramic Decoder | `PanoramicDecoder` | `said/models/panoramic_decoder.py` |
| Sph2Imaging | `Sph2Imaging` | `said/models/sph2imaging.py` |
| Multi-Scale Deformable Attention and Up Blocks | `MultiScaleFeaturePath` | `said/models/sph2imaging_blocks/multi_scale_features.py` |
| Class Feature Encoder | `PaSSTClassFeatureEncoder` or `AudioMAEClassFeatureEncoder` | `said/models/class_feature_encoders/` |
| Mask Decoder | `MaskDecoder` | `said/models/sph2imaging_blocks/mask_decoder.py` |
| Active Head | `MaskDecoder.active_head` | `said/models/sph2imaging_blocks/mask_decoder.py` |
| Map Head | `MapHead` | `said/models/sph2imaging_blocks/mask_decoder.py` |
| Class Head | `ClassHead` | `said/models/sph2imaging_blocks/class_head.py` |
| Refine Block | `RefineBlock` | `said/models/sph2imaging_blocks/refine_block.py` |

Public runtime objects and output fields use these paper terms. The public
loading interface is `said/models/loading/`; its compatibility
implementation translates historical serialized identifiers without changing
the source checkpoint.

## Final prediction contract

`SAID.forward` returns `SAIDOutput`. Its `labeled_acoustic_maps` field is a
`LabeledAcousticMaps` object containing the three tensors that jointly define
the paper's final predictions:

| Field | Shape | Meaning |
|---|---|---|
| `refined_maps` | `[B,T,16,180,360]` | Refine Block map values in `[0,1]` |
| `slot_class_ids` | `[B,T,16]` | Zero-based argmax Class Head IDs |
| `slot_confidence` | `[B,T,16]` | Confidence defined below |

The same tensors are available directly as `output.refined_maps`,
`output.slot_class_ids`, and `output.slot_confidence`. Intermediate Audio2Sph
and Sph2Imaging values remain available in `output.audio2sph` and
`output.sph2imaging` for training and analysis.

The 16 entries are candidate slots. A slot becomes a reported source only
after confidence thresholding and per-frame selection; the slot dimension does
not assert that 16 sources are active in every frame.

For slot `n`, confidence is

```text
Slot Active
× max softmax(Slot Class)
× mean refined-map value over pixels above 0.5.
```

The third factor is zero when no pixel exceeds 0.5. Class labels and confidence
leave the refined map values unchanged.

Class IDs use the following fixed zero-based order:

| ID | Class | ID | Class |
|---:|---|---:|---|
| 0 | Female speech | 7 | Door open/close |
| 1 | Male speech | 8 | Music |
| 2 | Clapping | 9 | Musical instr. |
| 3 | Telephone | 10 | Water tap/shower |
| 4 | Laughter | 11 | Bell |
| 5 | Domestic sounds | 12 | Knock |
| 6 | Walk/footsteps |  |  |

The same order is exported as `said.DCASE2026_CLASS_NAMES`.

## Recording domain

The PaSST checkpoint contains domain embeddings learned for the Sony and TAU
DCASE recording subsets. `RecordingDomain.SONY` maps to serialized ID 0 and
`RecordingDomain.TAU` maps to ID 1. The Python inference API and CLI use Sony
as the documented default. DCASE evaluation selects the domain from each
recording's subset metadata. SourceBank scenes pass `None`, so neither DCASE
domain embedding is added to their Class Features. AudioMAE accepts the same
argument and does not apply a domain embedding.

## Audio2Sph pretraining

Class-agnostic pretraining uses a separate wrapper:

```text
Audio2SphPretrainingModel
├── Audio2Sph → Y0
└── Panoramic Decoder
    └── Conv → upsample ×2 → Conv → upsample ×2 → Conv → Conv → sigmoid
```

The Panoramic Decoder reads Y0 directly, bypassing Time Alignment, and restores
the STFT frame count after spatial decoding. Complete SAID removes the
Panoramic Decoder and passes the time-aligned Panoramic Features to
Sph2Imaging. The published `audio2sph.ckpt` contains all 233 Audio2Sph tensors
and all 12 Panoramic Decoder tensors. `load_audio2sph_pretraining_checkpoint`
applies the same file fingerprint, submitted-schema, tensor shape/dtype, and
strict-load checks as the complete-model loader.

## Submitted checkpoint compatibility

The submitted exponential-moving-average checkpoints store flat state
dictionaries whose serialized identifiers predate the final paper terminology.
The compatibility boundary verifies each original file and maps its tensors to
the hierarchy above without rewriting the checkpoint.

The runtime SAID hierarchy excludes the Panoramic Decoder and the unused
recognition-classifier head. The submitted flat checkpoints still contain
those compatibility-only tensors. The loader first verifies the original file
and submitted schema, then maps only the active runtime tensors. The temporal
Class Feature Encoder backbone and every Class Feature Cross-Attention layer
remain active.

Checkpoint loading verifies file size, SHA256, the submitted component schema,
collision-free key normalization, and exact runtime key/shape/dtype equality
before calling `load_state_dict(strict=True)`.
