# Video Stream Compositor (FFmpeg PoC)

## Internship Project (2020)

This project was developed during a software engineering internship as a proof-of-concept to explore multi-stream video composition using FFmpeg.

The original use case involved a **remote assistance system for industrial environments**, where technicians wearing smart glasses could stream video to remote subject-matter experts (SMEs). The system required the ability to combine multiple video feeds, overlays, and audio sources into a single synchronized output.

This prototype demonstrates how **multiple media inputs can be aligned using timing metadata and merged into a composite output video**.

---

# Overview

The system merges multiple media inputs by reading a session configuration from a JSON file and constructing a timeline-based composition.

Each input file specifies:

* start time
* duration
* whether audio/video components should be included
* how the media should be processed

The pipeline then:

1. Parses the session configuration
2. Extracts audio and video components
3. Aligns streams based on start times
4. Composes video overlays for each time interval
5. Mixes audio streams
6. Produces a final merged video output

---

# System Pipeline

```
Input Media Files
       ↓
Session Configuration (data.json)
       ↓
Preprocessing & Timeline Segmentation
       ↓
Video Overlay Composition
       ↓
Audio Mixing
       ↓
Final Output Video
```

---

# Requirements

Tested on:

* Elementary OS 5.1 Hera
* Python 3.6

Dependencies:

```
pip install ffmpeg-python
sudo apt-get install ffmpeg
sudo apt-get install libavdevice57
```

---

# Running the Project

Clone the repository:

```
git clone https://github.com/Arnav-Ajay/video-stream-compositor-poc.git
cd video-stream-compositor-poc
```

Create the required directories:

```
mkdir files output temp
```

Place input media files in:

```
files/
```

Edit the session configuration:

```
data.json
```

Then run:

```
python3 main.py
```

The final merged video will be generated in:

```
output/
```

---

# Project Structure

```
├── data.json               # Session configuration describing input media
├── files/                  # Input media files
│   ├── background.png
│   ├── vid1.mp4
│   ├── vid2.mp4
│
├── main.py                 # Entry point for the compositor
├── preprocess.py           # Preprocessing and timeline generation
├── rules.py                # Media formatting and layout rules
│
├── interface/
│   ├── fileinterface.py    # File object abstraction
│   ├── sessioninterface.py # Session object abstraction
│   └── timeline.py         # Timeline interval structure
│
├── output/                 # Generated output videos
├── temp/                   # Temporary processing files
└── README.md
```

---

# Session Configuration (`data.json`)

Each session defines the set of media inputs and how they should be combined.

Example structure:

```json
{
  "session1": {
    "num_input": 4,
    "input_files": {
      "file1": {
        "filename": "LR1.mp4",
        "filepath": "./files/LR1.mp4",
        "filetype": "mp4",
        "start_time": 0,
        "duration": 240,
        "audio_c": true,
        "video_c": true,
        "flag": 0
      }
    },
    "duration": 306
  }
}
```

Key parameters:

| Field        | Description                             |
| ------------ | --------------------------------------- |
| `start_time` | Time offset (seconds) when media begins |
| `duration`   | Length of the media clip                |
| `audio_c`    | Include audio component                 |
| `video_c`    | Include video component                 |
| `flag`       | Controls how media is processed         |

Flag values:

```
0 → no change
1 → audio only
2 → video only
3 → skip file
```

---

# Core Modules

## preprocess.py

Handles session preparation and timeline generation.

Functions include:

* `get_data()` – Load session data from JSON
* `get_smallest_st()` – Find earliest start time
* `get_greatest_et()` – Find latest end time
* `get_audio_component()` – Extract audio streams
* `get_video_component()` – Extract video streams
* `check_num_audios()` – Mix audio streams when needed
* `create_sub_output()` – Generate video overlays for intervals
* `create_output()` – Assemble final output video
* `empty_temp()` – Clear temporary processing files

---

## rules.py

Defines formatting and layout rules.

Key functions:

* `convert_to_mono()` – Convert audio streams to mono
* `format_session()` – Normalize session configuration
* `get_dim()` – Determine output resolution layout
* `get_cord()` – Determine position of each video feed

---

## main.py

Entry point for the application.

Key functions:

* `check_num_running()` – Number of streams active at a given time
* `get_running_files()` – List active media files during an interval

---

# Data Model

## File Object

Represents an input media file.

Attributes:

```
filename
filepath
filetype
start_time
duration
audio_c
video_c
flag
```

---

## Session Object

Represents a composition session.

Attributes:

```
num_inputs
input_files
duration
```

---

## Timeline Object

Represents a segment of the video timeline.

Attributes:

```
id
start_time
duration
num_inputs
input_files
```

---

# Status

This repository contains an **early proof-of-concept developed during an internship in 2020**.

The goal of the project was to validate the feasibility of synchronizing and compositing multiple video streams using FFmpeg.

The implementation focuses on demonstrating the core pipeline rather than providing a production-ready system.

---

# License

MIT License
