# Diode ONE

A free neural distortion plugin by [Partial Audio](https://partialaudio.com), with Dist, Tone, input gain and
output gain controls driven by a single recurrent model trained on recordings of a classic hardware distortion pedal.

<!-- Screenshot and audio demo go here once the new UI exists. -->

## Download

Builds will be published on the [Releases](../../releases) page

## How it works

The model is a single-layer recurrent network (CoreAudioML `SimpleRNN`) that takes three inputs per sample:
the audio, the Dist position and the Tone position. It was trained on 25 recordings of the same dry signal
through the hardware at a 5 × 5 grid of knob settings, so the knobs sweep continuously rather than switching
between snapshots. Inference runs in real time with [RTNeural](https://github.com/jatinchowdhury18/RTNeural)
inside a [JUCE](https://juce.com/) plugin.

Diode ONE grew out of my thesis on neural emulation of a distortion pedal. The thesis, models and the evaluation are in
[ds1-neural-emulation](https://github.com/sathira10/ds1-neural-emulation) (tag `thesis-submission`).

## Repository layout

```
.
├── plugin/      JUCE 9 plugin: C++ source, embedded model, presets
└── training/    Python training pipeline (uv), CoreAudioML submodule, data manifest and scripts
```

## Plugin identifiers

| Setting           | Value                       |
|-------------------|-----------------------------|
| Product name      | Diode ONE                   |
| Bundle ID         | `com.partialaudio.diodeone` |
| Manufacturer code | `PaLK`                      |
| Plugin code       | `P005`                      |
| Version           | 0.1.0                       |
| Formats           | AU, VST3, Standalone        |

## Building

### Clone

```bash
git clone --recurse-submodules https://github.com/sathira10/pa-diode-one.git
cd pa-diode-one
```

### Plugin

Requires CMake 4.2+, and a C++23 toolchain.

```bash
cd plugin
cmake --preset release
cmake --build --preset release
```

CMake fetches JUCE 9.0.4 and RTNeural with CPM.

### Training

Requires Python 3.10+ and [uv](https://docs.astral.sh/uv/).

```bash
cd training
uv sync
```

The recordings are not stored in git. They are published as a GitHub Release asset and verified by sha256.

## Credits

Diode ONE uses [CoreAudioML](https://github.com/Alec-Wright/CoreAudioML) (GPLv3) for training,
[RTNeural](https://github.com/jatinchowdhury18/RTNeural) (BSD-3-Clause) for inference and
[JUCE](https://juce.com/).

The product is not affiliated with or endorsed by any pedal manufacturer. Any brand names mentioned refer only to
the hardware that was measured.
