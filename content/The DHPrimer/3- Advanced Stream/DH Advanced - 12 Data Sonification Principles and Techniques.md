---
title: "12: Data Sonification Principles and Techniques"
created: 2025-08-05T12:57:24.739Z
tags: [advanced]
---
# DH Advanced - 12: Data Sonification Principles and Techniques

While visualization represents data using visual elements (color, shape, position), **sonification** represents data using **sound**. It aims to leverage human auditory perception to understand patterns, trends, and outliers in data that might be complementary or alternative to visual inspection.

**Why Sonify?**

*   **Accessibility:** Provides an alternative for visually impaired users or situations where visual attention is limited.
*   **Complementary Insights:** Auditory perception excels at detecting temporal patterns, rhythms, and subtle changes over time that might be missed visually.
*   **Multivariate Data:** Sound has multiple dimensions (pitch, loudness, timbre, timing, spatial position) that can potentially map to multiple data dimensions simultaneously.
*   **Engagement:** Can provide a novel and potentially engaging way to experience data.

**Core Principle: Parameter Mapping Sonification (PMSon)**

The most common approach is **Parameter Mapping Sonification**. This involves establishing clear mappings between data dimensions and parameters of sound:

*   **Data Dimension** -> **Auditory Parameter**
    *   *Example:* Temperature -> Pitch (Higher temp = higher pitch)
    *   *Example:* Stock Price Change -> Loudness (Larger change = louder sound)
    *   *Example:* Event Occurrence -> Short Tone/Click (Timing)
    *   *Example:* Data Category -> Timbre (Different instruments/synthesizers for different categories)
    *   *Example:* Geographic Location -> Stereo Panning (Left/Right position)

**Key Considerations:**

*   **Clarity:** Is the mapping intuitive and easy to understand? Does the sound clearly reflect the data changes?
*   **Psychoacoustics:** How do humans perceive changes in pitch, loudness, etc.? These relationships are often non-linear.
*   **Aesthetics vs. Analytics:** Is the goal purely analytical (clear representation) or also aesthetic (pleasant or engaging sound)? These goals can sometimes conflict.
*   **Complexity:** Mapping too many data dimensions can lead to overly complex or confusing soundscapes.
*   **Context:** Provide context (e.g., baseline sounds, silence for missing data, clear start/end points).

**Simple Implementation Techniques:**

We can create basic sonifications using libraries for numerical processing and potentially simple sound generation or playback.

**Libraries:**

*   **NumPy:** For generating/manipulating numerical data representing sound waves.
*   **Matplotlib:** For visualizing the data being sonified.
*   **sounddevice (Optional):** For real-time audio playback. Requires system setup (e.g., PortAudio) and installation (`pip install sounddevice`).
*   **SciPy (Optional):** For writing WAV files (`scipy.io.wavfile`). (`pip install scipy`)
*   **IPython.display (Optional):** For embedding audio players.

**Example 1: Mapping a Time Series to Pitch**

Let's sonify a simple sine wave data series by mapping its value to the frequency (pitch) of a generated tone.

```python
import numpy as np
import matplotlib.pyplot as plt
import time
# Optional playback/saving
try:
    import sounddevice as sd
    sounddevice_available = True
except ImportError:
    sounddevice_available = False
    print("Sounddevice library not found. Real-time playback disabled.")
try:
    from scipy.io.wavfile import write as write_wav
    scipy_available = True
except ImportError:
    scipy_available = False
    print("Scipy library not found. Saving to WAV disabled.")
try:
    from IPython.display import Audio, display
    ipython_audio = True
except ImportError:
    ipython_audio = False


# --- Generate Sample Data (e.g., a sine wave) ---
sampling_rate = 44100 # Samples per second
data_duration = 5 # Seconds
t = np.linspace(0, data_duration, int(sampling_rate * data_duration), endpoint=False)
data_series = np.sin(2 * np.pi * 1 * t) # A 1 Hz sine wave
# Add some noise or variation
data_series += np.sin(2 * np.pi * 2.5 * t) * 0.3 + np.random.normal(0, 0.1, len(t))

print("--- Generated Data Series (first 10 points) ---")
print(data_series[:10])

# --- Plot the Data ---
plt.figure(figsize=(12, 3))
plt.plot(t, data_series)
plt.title("Data Series to Sonify")
plt.xlabel("Time (s)")
plt.ylabel("Data Value")
plt.grid(True)
plt.show()

# --- Sonification: Map Data to Pitch (Frequency) ---
# Define mapping range
min_freq = 220.0 # A3 note
max_freq = 880.0 # A5 note

# Normalize data to range [0, 1]
data_min = data_series.min()
data_max = data_series.max()
if data_max - data_min > 1e-6: # Avoid division by zero if data is flat
    normalized_data = (data_series - data_min) / (data_max - data_min)
else:
    normalized_data = np.full_like(data_series, 0.5) # Assign middle value if flat

# Map normalized data to frequency range (linear mapping)
frequencies = min_freq + normalized_data * (max_freq - min_freq)

# --- Generate Audio Signal ---
# Create a sine wave where the frequency changes according to 'frequencies'
# Need to calculate phase correctly for continuous frequency change
# Phase = integral of angular frequency (2 * pi * frequency)
phase = np.cumsum(2 * np.pi * frequencies / sampling_rate)
# Amplitude (can also be mapped, here constant)
amplitude = 0.5
audio_signal = amplitude * np.sin(phase)

print("\n--- Generated Audio Signal (first 10 samples) ---")
print(audio_signal[:10])

# --- Play or Save Audio ---
output_wav_file = "sonification_pitch.wav"

if sounddevice_available:
    try:
        print(f"\nPlaying sonification (mapping to pitch)... Duration: {data_duration}s")
        sd.play(audio_signal, samplerate=sampling_rate)
        time.sleep(data_duration + 0.5) # Wait for playback to finish
        sd.stop()
        print("Playback finished.")
    except Exception as e_play:
        print(f"Error during playback: {e_play}")
elif ipython_audio:
     print("\nDisplaying audio player (mapping to pitch)...")
     display(Audio(data=audio_signal, rate=sampling_rate))

if scipy_available:
    try:
        # Scale to 16-bit integer range for WAV file
        audio_signal_int16 = np.int16(audio_signal / np.max(np.abs(audio_signal)) * 32767)
        write_wav(output_wav_file, sampling_rate, audio_signal_int16)
        print(f"\nSaved sonification to '{output_wav_file}'")
    except Exception as e_save:
        print(f"Error saving WAV file: {e_save}")
elif not sounddevice_available and not ipython_audio:
     print("\nCannot play or save audio. Install sounddevice, scipy, or run in IPython.")

```

**Example 2: Mapping Categorical Data to Different Tones**

Let's represent different categories with distinct sine wave tones.

```python
# --- Sample Categorical Data ---
# Simulate categories changing over time
categories = ['A'] * 10 + ['B'] * 15 + ['C'] * 5 + ['A'] * 10 + ['B'] * 10
category_duration_samples = sampling_rate // 4 # Duration of each category's sound (0.25s)

# --- Define Mapping: Category -> Frequency ---
category_map = {
    'A': 261.63, # C4 (Middle C)
    'B': 329.63, # E4
    'C': 392.00  # G4
}
default_freq = 100 # Frequency for unknown categories

# --- Generate Audio Signal ---
categorical_audio_signal = np.array([])
print("\n--- Generating Categorical Sonification ---")
for category in categories:
    freq = category_map.get(category, default_freq)
    print(f"Category: {category}, Frequency: {freq:.2f} Hz")
    # Generate tone for this category
    t_cat = np.linspace(0, category_duration_samples / sampling_rate, category_duration_samples, endpoint=False)
    tone = 0.6 * np.sin(2 * np.pi * freq * t_cat) # Create sine wave for the duration
    # Add a short fade-out to avoid clicks (optional)
    fade_len = int(0.01 * sampling_rate) # 10ms fade
    if fade_len > 0 and len(tone) > fade_len:
       fade_out = np.linspace(1, 0, fade_len)
       tone[-fade_len:] *= fade_out

    categorical_audio_signal = np.concatenate((categorical_audio_signal, tone))

total_cat_duration = len(categorical_audio_signal) / sampling_rate
print(f"\nTotal duration of categorical sonification: {total_cat_duration:.2f}s")

# --- Play or Save Audio ---
output_cat_wav_file = "sonification_category.wav"
if sounddevice_available:
    try:
        print("\nPlaying categorical sonification...")
        sd.play(categorical_audio_signal, samplerate=sampling_rate)
        time.sleep(total_cat_duration + 0.5)
        sd.stop()
        print("Playback finished.")
    except Exception as e_play:
        print(f"Error during playback: {e_play}")
elif ipython_audio:
     print("\nDisplaying audio player (categorical)...")
     display(Audio(data=categorical_audio_signal, rate=sampling_rate))


if scipy_available:
    try:
        audio_signal_int16 = np.int16(categorical_audio_signal / np.max(np.abs(categorical_audio_signal)) * 32767)
        write_wav(output_cat_wav_file, sampling_rate, audio_signal_int16)
        print(f"\nSaved categorical sonification to '{output_cat_wav_file}'")
    except Exception as e_save:
        print(f"Error saving WAV file: {e_save}")
elif not sounddevice_available and not ipython_audio:
     print("\nCannot play or save audio. Install sounddevice, scipy, or run in IPython.")

```

**Further Exploration:**

*   **More Parameters:** Map data to loudness, panning (stereo position), or use more complex timbres (e.g., combining multiple sine waves, using pre-recorded samples).
*   **Libraries:** Explore dedicated sonification libraries like `PySonar` or `SonifyPy` (check current status/maintenance), or use music synthesis environments like SuperCollider or ChucK controlled via OSC from Python.
*   **Auditory Graphs:** Map graph structures (nodes, edges, metrics) to sound.

Sonification offers a unique sensory channel for data exploration in DH, particularly valuable for time-series data, event detection, and providing alternative access to information. Careful design of the data-to-sound mapping is crucial for effectiveness.

➡️ **Next Step:** [[DH Advanced - Wrap-up and Further Directions]]

---

*Back to [[Welcome to the DHP]] | [[DH Advanced - Start Here]]*