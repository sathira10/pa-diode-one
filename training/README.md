# Diode ONE training

Python pipeline that trains one recurrent model conditioned on the Dist and Tone knobs and exports it for RTNeural.

## Setup

```bash
git submodule update --init --recursive   # CoreAudioML
uv sync
```

## Data

The 25 recordings (a 5 × 5 Dist × Tone grid, all from the same dry signal) are not stored in git. They are
published as a GitHub Release asset and listed in `data/manifest.csv`:

| Column     | Meaning                                         |
|------------|-------------------------------------------------|
| `take`     | Take id                                         |
| `dist`     | Dist knob position, 0 to 1                      |
| `tone`     | Tone knob position, 0 to 1                      |
| `filename` | File name inside the release asset              |
| `sha256`   | Checksum of the file                            |

The dry input signal gets its own row with empty `dist` and `tone`.

The recordings go in `data/audio/`, which git ignores.

## Layout

```
training/
├── configs/        conditioned LSTM / GRU configs
├── data/
│   ├── manifest.csv
│   └── audio/      fetched recordings (git-ignored)
├── scripts/        fetch_data.py, align_check.py, train.py, evaluate.py, export_rtneural.py
└── CoreAudioML/    submodule, pinned
```

CoreAudioML is by Alec Wright and licensed under GPLv3.
