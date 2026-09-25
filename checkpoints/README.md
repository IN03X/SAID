# Checkpoints

`said demo`, `said infer`, `said evaluate`, and the published training recipes
download or load their selected published checkpoint in this directory. The
canonical filenames are:

```text
said_passt.ckpt
said_audiomae.ckpt
audio2sph.ckpt
```

The SourceBank recipes use `audio2sph.ckpt` for Audio2Sph initialization and
the selected complete SAID checkpoint for Class Feature Encoder
initialization. The DCASE recipes initialize from the selected complete SAID
checkpoint.

Downloaded `.ckpt`, partial-download, and lock files are excluded from version
control.
