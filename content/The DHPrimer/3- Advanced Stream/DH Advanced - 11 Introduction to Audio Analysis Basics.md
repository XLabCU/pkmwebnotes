---
title: "11: Introduction to Audio Analysis Basics"
created: 2025-08-05T12:57:24.739Z
tags: [advanced]
---
# DH Advanced - 11: Introduction to Audio Analysis Basics

Beyond text and images, audio recordings – oral histories, music, environmental sounds, spoken word performances – are increasingly important data sources in the humanities. Computational audio analysis allows us to process, analyze, and extract features from sound signals.

**Core Concepts:**

*   **Digital Audio:** Sound is represented as a sequence of numerical samples taken at regular intervals.
    *   **Waveform:** A plot of sample amplitude (loudness/pressure level) over time. This is the raw representation.
    *   **Sampling Rate (sr):** How many samples are taken per second (measured in Hertz, Hz). Common values are 44100 Hz (CD quality), 48000 Hz, 22050 Hz, 16000 Hz (common for speech). Higher sampling rates capture higher frequencies. *Nyquist Theorem:* Need sr >= 2 * max_frequency to capture a frequency.
    *   **Bit Depth:** Number of bits used to represent each sample's amplitude. Higher bit depth means greater dynamic range (difference between loudest and quietest sounds). Common values: 16-bit, 24-bit.
    *   **Channels:** Mono (1 channel), Stereo (2 channels), etc.
*   **Time Domain:** Analyzing the waveform directly over time (e.g., amplitude).
*   **Frequency Domain:** Analyzing the frequency content of the audio signal, often using the **Fourier Transform**. This tells us *which* frequencies are present and how strong they are.
    *   **Spectrogram:** A visual representation of frequency content over time. Typically plots time on the x-axis, frequency on the y-axis, and intensity/amplitude of each frequency at each time point using color/brightness. Calculated using the **Short-Time Fourier Transform (STFT)**, which analyzes short, overlapping windows of the signal.

**Libraries:**

*   **Librosa:** The standard Python library for audio and music analysis. Provides easy loading, feature extraction, visualization, and more. (`pip install librosa`)
*   **Matplotlib:** For plotting waveforms and spectrograms.
*   **NumPy:** For numerical operations on audio data arrays.
*   **IPython.display (Optional):** For embedding audio players directly in notebooks like Jupyter. (`pip install ipython`) or `sounddevice` (`pip install sounddevice`) for real-time playback (requires system setup like PortAudio).

**Setup: Loading and Displaying Audio:**

You'll need an audio file (e.g., `.wav`, `.mp3`, `.ogg`). For formats other than WAV, `librosa` might require `ffmpeg` to be installed on your system. You can find Creative Commons audio samples online (e.g., Freesound.org) or use short recordings. Let's assume `sample_audio.wav`.

```python
import librosa
import librosa.display # For plotting helpers
import matplotlib.pyplot as plt
import numpy as np
import os
# Optional: for audio playback in some environments
try:
    from IPython.display import Audio, display
    ipython_audio = True
except ImportError:
    ipython_audio = False
    print("IPython not found. Audio playback in notebook might not work.")
    # Alternatively, setup sounddevice if needed/possible

# --- Configuration ---
audio_filename = "sample_audio.wav" # Replace with your audio file

# --- Check if file exists ---
if not os.path.exists(audio_filename):
    print(f"Error: Audio file '{audio_filename}' not found.")
    # Add code to download a sample if needed, e.g., from librosa examples
    # try:
    #     print("Downloading a sample audio file...")
    #     audio_filename = librosa.ex('trumpet') # Librosa example file
    #     print(f"Using librosa example: {audio_filename}")
    # except Exception as download_err:
    #     print(f"Failed to get librosa sample: {download_err}")
    #     audio_filename = None
else:
    print(f"Using audio file: '{audio_filename}'")


# --- Load Audio with Librosa ---
if audio_filename and os.path.exists(audio_filename):
    try:
        # Load audio file. librosa converts to mono and float32 by default.
        # sr=None preserves the original sampling rate. Specify sr=desired_rate to resample.
        y, sr = librosa.load(audio_filename, sr=None)

        print("\n--- Audio Information ---")
        print(f"Sampling Rate (sr): {sr} Hz")
        print(f"Number of samples: {len(y)}")
        duration = librosa.get_duration(y=y, sr=sr)
        print(f"Duration: {duration:.2f} seconds")
        print(f"Audio data shape: {y.shape}") # Should be 1D array (mono)
        print(f"Audio data type: {y.dtype}") # Should be float32

        # --- Optional: Play Audio ---
        if ipython_audio:
            print("\n--- Playing Audio Snippet (first 5 seconds) ---")
            display(Audio(data=y[:sr*5], rate=sr)) # Play first 5 seconds
        else:
            print("\n(Skipping audio playback - IPython.display.Audio not available)")


        # --- Plot Waveform ---
        plt.figure(figsize=(14, 5))
        librosa.display.waveshow(y, sr=sr, alpha=0.8)
        plt.title(f'Waveform of {os.path.basename(audio_filename)}')
        plt.xlabel('Time (s)')
        plt.ylabel('Amplitude')
        plt.grid(True, axis='y')
        plt.tight_layout()
        plt.show()

    except Exception as e:
        print(f"An error occurred processing the audio: {e}")
        y, sr = None, None # Ensure variables are None if loading fails
else:
    print("\nCannot proceed without a valid audio file.")
    y, sr = None, None

```

**Basic Feature Extraction:**

Librosa makes extracting common features straightforward.

```python
if y is not None and sr is not None:
    print("\n--- Basic Feature Extraction ---")

    # 1. Zero-Crossing Rate (ZCR)
    #    Rate at which the signal changes sign (crosses zero).
    #    Related to noisiness or dominant frequency (higher ZCR for higher frequencies/noise).
    zcr = librosa.feature.zero_crossing_rate(y)
    print(f"Zero-Crossing Rate shape: {zcr.shape}") # (1, num_frames)
    # Average ZCR
    avg_zcr = np.mean(zcr)
    print(f"Average Zero-Crossing Rate: {avg_zcr:.4f}")

    # 2. Root Mean Square (RMS) Energy
    #    Measure of short-term power/loudness in the signal.
    rms = librosa.feature.rms(y=y)
    print(f"RMS Energy shape: {rms.shape}") # (1, num_frames)
    avg_rms = np.mean(rms)
    print(f"Average RMS Energy: {avg_rms:.4f}")

    # --- Plot RMS Energy over Waveform ---
    plt.figure(figsize=(14, 5))
    # Calculate time axis for features (frames to time)
    times = librosa.times_like(rms, sr=sr)
    librosa.display.waveshow(y, sr=sr, alpha=0.4, label='Waveform')
    plt.plot(times, rms[0], label='RMS Energy', color='r') # rms is 2D, take first row
    plt.title('Waveform and RMS Energy')
    plt.xlabel('Time (s)')
    plt.ylabel('Amplitude / RMS')
    plt.legend()
    plt.grid(True)
    plt.tight_layout()
    plt.show()


    # 3. Spectrogram (using STFT)
    print("\n--- Calculating Spectrogram ---")
    # STFT converts signal into time-frequency representation
    stft_result = librosa.stft(y)
    # Convert amplitude spectrogram to dB scale (log magnitude) - often better for visualization
    S_db = librosa.amplitude_to_db(np.abs(stft_result), ref=np.max)
    print(f"Spectrogram shape (Frequency bins, Time frames): {S_db.shape}")

    # --- Plot Spectrogram ---
    plt.figure(figsize=(14, 5))
    # sr and hop_length are needed to label axes correctly
    librosa.display.specshow(S_db, sr=sr, x_axis='time', y_axis='log') # Use log frequency scale
    plt.colorbar(format='%+2.0f dB')
    plt.title('Log-Frequency Spectrogram')
    plt.tight_layout()
    plt.show()

else:
    print("\nSkipping feature extraction because audio data (y, sr) is not available.")

```

**Other Common Features (Brief Mention):**

*   **Mel-Frequency Cepstral Coefficients (MFCCs):** Widely used features in speech recognition and music information retrieval, representing the short-term power spectrum based on a perceptually motivated frequency scale (Mel scale). `librosa.feature.mfcc()`
*   **Chroma Features:** Represent the intensity of the 12 standard pitch classes (C, C#, D, ... B). Useful for analyzing harmony in music. `librosa.feature.chroma_stft()`
*   **Spectral Centroid:** Indicates the "center of mass" of the spectrum, related to perceived brightness. `librosa.feature.spectral_centroid()`
*   **Tempo and Beat Tracking:** Estimating the tempo (beats per minute) and locating beat positions. `librosa.beat.beat_track()`

**Applications in DH:**

*   Analyzing speech patterns (pitch, rhythm, pauses) in oral histories.
*   Comparing musical performances or genres based on features like tempo, harmony (chroma), or timbre (MFCCs, spectral centroid).
*   Identifying sound events in field recordings.
*   Creating inputs for machine learning models for audio classification (e.g., genre recognition, speaker identification).

Librosa provides a powerful toolkit for moving beyond simply listening to audio and starting to analyze its quantitative properties.

➡️ **Next Step:** [[DH Advanced - 12 Data Sonification Principles and Techniques]]

---

*Back to [[Welcome to the DHP]] | [[DH Advanced - Start Here]]*