# Demonstration inputs

These files are the four fixed inputs used by `said demo`. PaSST, AudioMAE,
and Audio2Sph write model-specific files into the shared `said_demo_output/`
directory: `said_passt_demo_*.mp4`, `said_audiomae_demo_*.mp4`, and
`audio2sph_demo_*.mp4`. Generated videos are not part of the source or wheel.

| Directory | Domain | Official recording | Interval |
|---|---|---|---:|
| `said_demo_1/` | TAU | `fold4_room8_mix003` | 150--170 s |
| `said_demo_2/` | TAU | `fold4_room10_mix007` | 50--70 s |
| `said_demo_3/` | Sony | `fold4_room24_mix002` | 20--40 s |
| `said_demo_4/` | Sony | `fold4_room23_mix003` | 0--20 s |

Each directory contains:

- `recording.wav`: the official 24 kHz PCM samples from Eigenmike channels
  6, 10, 26, and 22, in that order;
- `ground_truth.json.gz`: official annotations whose frame indices fall in the
  half-open interval shown above; original frame indices and annotation values
  are retained;
- `panorama.mp4`: the synchronized official panorama interval, re-encoded as
  H.264 without an audio stream for portable playback.

Audio and labels originate from STAIRS26, DOI
[10.5281/zenodo.18171005](https://doi.org/10.5281/zenodo.18171005).
Panorama video originates from STARSS23, DOI
[10.5281/zenodo.7880637](https://doi.org/10.5281/zenodo.7880637).
The cited deposits declare the MIT License. Copyright is held by Sony and
Tampere University. The applicable license texts are included as
`LICENSES/STAIRS26-MIT.txt` and `LICENSES/STARSS23-MIT.txt`.

The exact preparation procedure is retained in the release audit record as
`prepare_packaged_demo_inputs.py`. File identities are:

```text
1ac7c6528b9704d4d6e4eba6b76c64d2bc6b751d28e3a4ac363c06bb78263e87  said_demo_1/ground_truth.json.gz
014401c81dbb0e9634478d799b87359d152dc7d2050f884e69f78ee8cdf8044b  said_demo_1/panorama.mp4
8dd4e37b6e4e862e44a2adf5204b0c30052276d360cc6ccec040c4e63290d56c  said_demo_1/recording.wav
623c9ce89cb6bcbd331fe8c876097b8f5201967d79d9a75e556b3dc26af96279  said_demo_2/ground_truth.json.gz
ae835262f650ba8e272f3ea93caecb6c8f50f047da49715345273256f989dca3  said_demo_2/panorama.mp4
0309252074da5aeb99d916b77d3645c7e01b3297020988f0f50915caea72e227  said_demo_2/recording.wav
c4a2d75f621d4dd6ce447510ef4fe3874ffe1d42c765d430c4921b8ae02aa2ff  said_demo_3/ground_truth.json.gz
6345db7f061444c2c4cc6d71314611a9a292cce71414c0abde0cbdb6f9cebe5e  said_demo_3/panorama.mp4
4994ed0f082ba9d97edf6da0bd522b0dc9e32ae1a35af489d0cdcef0bc811ec6  said_demo_3/recording.wav
c27ecd05c49a93d72cad7746887cf5c88879da55316eae9c08c0e335c4c14394  said_demo_4/ground_truth.json.gz
f8fb2b6367d4fd6f890bfa45f0499412de30547d641036785a9b7742a2912fd3  said_demo_4/panorama.mp4
064dc38cacc020c951206a0c6b8e60af95219e30fb1882e9b4887b95d6fe8f86  said_demo_4/recording.wav
```
