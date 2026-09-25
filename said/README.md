# SAID source package

The model implementation begins in `models/said.py`: `models/audio2sph.py`
implements Audio2Sph and `models/sph2imaging.py` implements Sph2Imaging.

- `inference/` contains audio loading, complete-recording inference, result
  saving, demo generation, and visualization.
- `evaluation/` contains DCASE output conversion, full evaluation, and metric
  integration.
- `training/` contains the public training workflow; `data/` contains the
  DCASE2026 taxonomy and training/development adapters, SourceBank, and
  simulated-scene data interfaces.
- `rendering/` contains Online Scene Generation.
- `arrays/` contains microphone-array geometry definitions used by rendering
  and supports additional arrays with the same geometry schema.
- `demo_data/` contains the four licensed input excerpts used by `said demo`;
  the repository README presents Demo 1 and Demo 3, and the command generates
  all four comparison videos as user outputs.
- `inference/demo.py` validates these inputs and generates their Ground Truth
  views.
- `utils/` contains shared configuration loading, checkpoint download, and
  optional prediction-compression utilities.

`cli.py` connects these components to the `said` command.
