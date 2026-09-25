# Paper-checkpoint training-data notices

This document records the training-data boundary for the official SAID
(PaSST), SAID (AudioMAE), and Audio2Sph + Panoramic Decoder checkpoints. The software includes four
documented 20-second STAIRS26/STARSS23 demonstration excerpts. It includes no
complete source recording, training collection, SourceBank manifest, or
dataset archive.

All three checkpoints are distributed for non-commercial research use only.
The SAID software can be used separately to train independent weights with
model components and datasets selected by the user for their intended use.

## SourceBank identity

The paper SourceBank index contained 122,359 rows and has SHA256
`489f2405025d64f087a76a753cb0fae1051e91df00236ae2285ca2046d664535`.
It referenced locally obtained recordings without copying audio into the
index.

| Source | Manifest rows | Unique attributed items | Terms represented |
|---|---:|---:|---|
| CSTR VCTK Corpus 0.80 | 43,832 | corpus-level notice | ODC Attribution 1.0 |
| MUSDB18-HQ | 70,416 | 150 tracks | restricted educational-use tracks, CC BY-NC-SA, and CC BY-NC-SA 3.0 |
| FSD50K | 4,672 | 4,505 clips | CC0, CC BY, CC BY-NC, and CC Sampling+ |
| FSDKaggle2018 | 3,439 | 3,439 clips | CC0, CC BY, and one CC BY-NC clip |

The selected FSD50K clips comprise 2,038 CC0 clips, 1,865 CC BY clips,
459 CC BY-NC clips, and 143 CC Sampling+ clips. The selected FSDKaggle2018
clips comprise 1,366 CC0 clips, 2,072 CC BY clips, and one CC BY-NC clip.

Each separately distributed paper-checkpoint bundle includes the
machine-readable `paper_checkpoint_source_attributions.tsv` record. It contains
stable source identifiers, creator or artist attribution where supplied,
license labels, and official source pages. It contains no local filesystem
paths or audio. Its SHA256 is
`4e8694129b70ea0db1f01943e36fc9de1e58a92d3644724b921f9ccbd8bb0c23`.

## CSTR VCTK Corpus 0.80

Contains information from the CSTR VCTK Corpus, which is made available under
the Open Data Commons Attribution License 1.0:
<https://opendatacommons.org/licenses/by/1-0/>.

The corpus is copyright 2012, Centre for Speech Technology Research,
University of Edinburgh, and was constructed by Christophe Veaux, Junichi
Yamagishi, and Kirsten MacDonald. Official record:
<https://datashare.ed.ac.uk/handle/10283/2651>.

## MUSDB18-HQ

MUSDB18-HQ is provided for educational purposes, with commercial use of its
recordings requiring permission from the applicable copyright holders. The
official record identifies 100 DSD/Mixing Secrets tracks and two Native
Instruments tracks as restricted, 46 MedleyDB tracks under CC BY-NC-SA, and
two C't Remix Competition tracks under CC BY-NC-SA 3.0.

- Official dataset record: <https://doi.org/10.5281/zenodo.3338373>
- Official license and track information:
  <https://sigsep.github.io/datasets/musdb.html>
- Official per-track list:
  <https://github.com/sigsep/website/blob/master/content/datasets/assets/tracklist.csv>

The paper checkpoints contain learned model parameters and do not contain or
produce the source songs, stems, or waveforms.

## FSD50K

FSD50K as a curated dataset is released under CC BY 4.0. Each Freesound clip
retains its clip-level license. The selected clip IDs, uploaders, licenses, and
source pages are listed in the attribution TSV.

- Official record: <https://doi.org/10.5281/zenodo.4060432>
- Dataset license: <https://creativecommons.org/licenses/by/4.0/>

## FSDKaggle2018

FSDKaggle2018 as a curated dataset is released under CC BY 4.0. Each
Freesound clip retains its clip-level license. The selected Freesound IDs,
creator attribution where supplied, license labels, and source pages are
listed in the attribution TSV.

- Official release documentation:
  <https://zenodo.org/records/2552860>
- Dataset license: <https://creativecommons.org/licenses/by/4.0/>

## DCASE2026 development training data

Final paper-model fine-tuning used the DCASE2026 Task 3 development-training
recordings and labels. The cited official deposits declare the MIT License for
their corresponding versions:

- STAIRS26: <https://doi.org/10.5281/zenodo.18171005>
- STARSS23: <https://doi.org/10.5281/zenodo.7880637>

The dataset files and labels are not redistributed by SAID.

## Commercial training

The published checkpoints and derivatives of them are not licensed for
commercial use. A commercial user may train independent weights with the SAID
software after selecting a Class Feature Encoder, initialization, and training
corpora whose licenses permit that user's intended commercial activity.
