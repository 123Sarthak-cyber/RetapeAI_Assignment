# Voicemail Drop Compliance System

A Python-based system for detecting optimal timing to drop compliant voicemail messages after consumer greetings. This system ensures regulatory compliance by determining when to start playing prerecorded messages based on beep detection, silence analysis, and speech pattern recognition.

## 🎯 Problem Statement

When calling customers who don't answer, systems must leave voicemail messages that comply with regulations. The challenge is determining **exactly when** to start playing the message to ensure the consumer hears:
- ✅ The company name
- ✅ The return phone number

**Key constraint**: If a beep occurs, anything spoken before it is inaudible to the consumer.

## 🚀 Features

- **Multi-Signal Detection**: Combines beep detection, silence analysis, and speech pattern recognition
- **Beep Detection**: Uses frequency analysis (STFT) to identify characteristic beep sounds
- **Silence Detection**: Monitors for pauses indicating greeting completion
- **Speech Analysis**: Recognizes common end-of-greeting phrases
- **Real-time Streaming**: Simulates real-time audio processing
- **Visualization**: Generates waveform and spectrogram plots with detected events
- **Configurable Parameters**: Easily adjust thresholds and detection criteria

## 📋 Requirements

```
numpy>=1.21.0
scipy>=1.7.0
matplotlib>=3.3.0
```

## 🔧 Installation

### Clone the Repository

```bash
git clone https://github.com/yourusername/voicemail-drop-detector.git
cd voicemail-drop-detector
```

### Install Dependencies

```bash
pip install -r requirements.txt
```

## 💻 Usage

### Basic Usage

```python
from voicemail_detector import VoicemailDetector

# Initialize detector
detector = VoicemailDetector()

# Process an audio file
result = detector.process_audio_file(
    'path/to/greeting.wav',
    transcript="Hi, you've reached John. Leave a message after the beep.",
    visualize=True
)

# Get drop timestamp
drop_time = result['drop_timestamp']
print(f"Start playing message at: {drop_time}s")
```

### Batch Processing

```python
from voicemail_detector import process_directory

# Process all files in a directory
results = process_directory(
    input_dir='audio_files',
    output_dir='output',
    transcripts={
        'greeting1.wav': 'Transcript here...',
        'greeting2.wav': 'Another transcript...'
    },
    visualize=True
)
```

### Real-time Streaming Simulation

```python
from voicemail_detector import AudioStreamSimulator

# Simulate streaming
simulator = AudioStreamSimulator('greeting.wav', chunk_duration=0.1)

while True:
    chunk = simulator.get_next_chunk()
    if chunk is None:
        break
    
    audio_data, current_time = chunk
    # Process chunk in real-time
```

### Command Line

```bash
# Place audio files in 'audio_files' directory
mkdir audio_files
# Copy your .wav files here

# Run analysis
python voicemail_detector.py

# Results will be in 'output' directory
```

## 📊 Output Format

Each analysis produces:

```json
{
  "file": "greeting.wav",
  "duration": 8.5,
  "sample_rate": 44100,
  "drop_timestamp": 7.8,
  "confidence": "high",
  "reasoning": "Beep detected - starting after beep + safety buffer",
  "analysis": {
    "beep_detected": true,
    "beep_end_time": 7.3,
    "end_phrase_detected": true,
    "silence_detected_at": 7.5,
    "transcript": "Hi, you've reached Mike..."
  }
}
```

## 🔍 How It Works

### Detection Pipeline

1. **Beep Detection** (Primary)
   - Analyzes frequency spectrum (800-1200 Hz)
   - Validates duration (0.3-2.0 seconds)
   - If detected: `drop_time = beep_end + 0.5s`

2. **Silence Detection** (Secondary)
   - Monitors amplitude levels
   - Identifies pauses >300ms
   - If no beep: `drop_time = silence_start + 0.2s`

3. **Speech Pattern Analysis** (Supporting)
   - Recognizes end phrases ("after the beep", "leave a message")
   - Validates greeting completion

4. **Fallback Strategy**
   - Conservative timing based on duration
   - Ensures compliance even with ambiguous signals

### Decision Tree

```
Is beep detected?
├─ YES → Drop at beep_end + 0.5s (HIGH confidence)
└─ NO → Is silence detected?
    ├─ YES → Drop at silence_start + 0.2s (MEDIUM confidence)
    └─ NO → Is end phrase detected?
        ├─ YES → Drop at 85% of duration (MEDIUM confidence)
        └─ NO → Drop at 80% of duration (LOW confidence)
```

## 🎛️ Configuration

Customize detection parameters:

```python
config = {
    'silence_threshold': 0.02,        # Amplitude threshold
    'min_silence_duration': 0.3,      # Minimum silence (seconds)
    'beep_freq_range': (800, 1200),   # Beep frequency range (Hz)
    'beep_duration_range': (0.3, 2.0),# Beep duration (seconds)
    'safety_buffer': 0.5,             # Post-beep buffer (seconds)
    'end_phrases': [                  # Custom end phrases
        "after the beep",
        "leave a message",
        # Add more...
    ]
}

detector = VoicemailDetector(config=config)
```

## 📈 Visualization

The system generates visualizations showing:
- Audio waveform with detected events
- Spectrogram with frequency analysis
- Drop time marker (green line)
- Beep detection (red line)
- Silence detection (blue line)

## 🧪 Testing

Run unit tests:

```bash
python -m pytest tests/
```

Run with test audio files:

```bash
python examples/demo.py
```

## 🔄 Integration

### With Speech-to-Text Services

```python
# Example with Deepgram (pseudo-code)
from deepgram import Deepgram

async def process_with_stt(audio_path):
    # Get transcript
    dg_client = Deepgram(API_KEY)
    transcript = await dg_client.transcription.prerecorded(audio_path)
    
    # Analyze with transcript
    detector = VoicemailDetector()
    result = detector.process_audio_file(
        audio_path,
        transcript=transcript['results']['channels'][0]['alternatives'][0]['transcript']
    )
    
    return result
```

### With Telephony Systems

```python
# Real-time integration (pseudo-code)
def on_voicemail_detected(call_id, audio_stream):
    detector = VoicemailDetector()
    
    # Process stream
    # ... streaming logic ...
    
    # When drop_time reached
    play_compliance_message(call_id, "clearpath_message.wav")
```

## 📝 Project Structure

```
voicemail-drop-detector/
├── voicemail_detector.py      # Main detector class
├── tests/
│   ├── test_detector.py       # Unit tests
│   └── test_audio_samples/    # Test audio files
├── examples/
│   ├── demo.py                # Demo script
│   └── streaming_demo.py      # Streaming example
├── audio_files/               # Input directory (create this)
├── output/                    # Output directory (auto-created)
├── requirements.txt           # Dependencies
├── README.md                  # This file
└── LICENSE                    # License file
```

## 🤝 Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🙏 Acknowledgments

- Built for ClearPath Finance voicemail compliance
- Inspired by TCPA and consumer protection regulations
- Uses scipy for signal processing and numpy for numerical operations

## 📧 Contact

For questions or issues, please open a GitHub issue or contact:
- Email: your.email@example.com
- GitHub: [@yourusername](https://github.com/yourusername)

## 🔗 Links

- [Assignment Details](https://drive.google.com/drive/folders/1RnRAkMxQTwsD5w3Kzd3BawpiLes2GOqn)
- [Documentation](https://github.com/yourusername/voicemail-drop-detector/wiki)
- [Issues](https://github.com/yourusername/voicemail-drop-detector/issues)

---

**Note**: This system is designed for legitimate business purposes and regulatory compliance. Always ensure you follow local telecommunications regulations when using automated calling systems.
