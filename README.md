# 🎙️ Voicemail Compliance System

An automated voicemail drop system that detects optimal message placement timing using beep detection and silence analysis.

## 📋 Overview

The Voicemail Compliance System analyzes voicemail greetings to detect the ideal moment to drop a pre-recorded compliance message. It uses two detection methods:
- **Beep Detection**: Identifies the characteristic 1000Hz tone that signals message recording
- **Silence Detection**: Recognizes extended pauses (1200ms+) that indicate the greeting has ended

## 🚀 Features

- **Dual Detection System**: Combines frequency analysis and silence detection for robust trigger identification
- **Audio Processing**: Real-time FFT analysis with Hamming window for accurate beep detection
- **Automated Reporting**: Generates formatted audit reports with timestamp and trigger method
- **Demo Generation**: Creates combined audio samples showing message placement
- **Batch Processing**: Handles multiple voicemail files in a single execution

## 📦 Installation

```bash
# Install required dependencies
pip install numpy scipy pydub

# Install ffmpeg for audio processing
apt-get install ffmpeg  # Linux/Ubuntu
# brew install ffmpeg   # macOS
```

## 🔧 Usage

### Basic Processing

```python
from voicemail_compliance import VoicemailComplianceSystem

# Initialize the system
system = VoicemailComplianceSystem(chunk_size_ms=100)

# Process a single voicemail file
timestamp, method = system.process_file('voicemail_greeting.wav')

if timestamp:
    print(f"Message should drop at: {timestamp}s using {method}")
else:
    print(f"Requires: {method}")
```

### Batch Processing

```python
import glob
import os

# Process all WAV files in a directory
wav_files = sorted(glob.glob("*.wav"))
system = VoicemailComplianceSystem()

for path in wav_files:
    timestamp, method = system.process_file(path)
    print(f"{os.path.basename(path)}: {timestamp}s ({method})")
```

### Generate Demo Audio

```python
from demo_generator import generate_demo_audio

# Combine greeting + compliance message at detected timestamp
generate_demo_audio(
    input_file='vm1_output.wav',
    message_file='clearpath_message.mp3',
    trigger_time_s=13.1
)
```

## ⚙️ Configuration

The system can be customized with the following parameters:

```python
system = VoicemailComplianceSystem(
    chunk_size_ms=100  # Analysis window size in milliseconds
)

# Tunable detection parameters
system.BEEP_FREQ = 1000              # Target beep frequency (Hz)
system.FREQ_TOLERANCE = 50           # Frequency detection range (±Hz)
system.SILENCE_THRESHOLD = 0.01      # RMS volume threshold
system.REQUIRED_SILENCE_MS = 1200    # Minimum silence duration (ms)
```

## 📊 Output Format

### Audit Report
```
================================================================================
CLEARPATH FINANCE - VOICEMAIL DROP AUDIT REPORT
================================================================================
FILE NAME                 | START TIME   | TRIGGER METHOD
--------------------------------------------------------------------------------
vm1_output.wav           | 13.1s        | Beep Detection
  > ACTION: Playing Message: "Hi, this is ClearPath Finance..."

vm2_output.wav           | 8.5s         | Silence Detection
  > ACTION: Playing Message: "Hi, this is ClearPath Finance..."
```

## 🔍 Detection Methods

### Beep Detection
- Uses Fast Fourier Transform (FFT) with Hamming window
- Identifies 1000Hz ±50Hz frequency peaks
- Triggers message drop 400ms after beep detection

### Silence Detection
- Calculates RMS (Root Mean Square) volume per chunk
- Monitors continuous silence duration
- Triggers after 1200ms of silence below threshold

## 📁 Project Structure

```
voicemail-compliance-system/
├── voicemail_compliance.py    # Main system class
├── demo_generator.py          # Audio combination utility
├── requirements.txt           # Python dependencies
├── README.md                  # This file
└── examples/
    ├── vm1_output.wav         # Sample voicemail greeting
    └── clearpath_message.mp3  # Compliance message audio
```

## 🧪 Requirements

- Python 3.7+
- NumPy >= 1.19.0
- SciPy >= 1.5.0
- PyDub >= 0.25.0
- FFmpeg (system dependency)

Made by:
## Sarthak Mazumder  
Email Id - sarthakmazumder0901@gmail.com  
phone no - 6297475467

