# BEAT Bandroom

**A local browser audio tool for taming harsh high frequencies in Suno-generated music and adjusting tone and loudness.**

[한국어](README.md)

## Why I built it

I enjoy listening to music, but I do not have a background in writing lyrics or composing music. I wanted to address a listening problem I had noticed without having professional music-production knowledge, so I chose vibe coding: describing the features I wanted and implementing them with AI assistance.

As someone who loves listening to music, I find Suno AI to be a wonderful service. However, in my own experience, no matter which prompts I tried, the characteristic noise I noticed was never sufficiently resolved. Wanting to enjoy the music more comfortably became the motivation for building this application.

While making music with Suno AI, I noticed that some tracks had sizzling, sharp, or piercing high-frequency sounds that I perceived as noise. I wanted to make those tracks more comfortable to listen to, so I built this project through **vibe coding**: describing what I wanted, implementing it with AI coding assistance, testing the results, and refining the behavior.

The original motivation was to reduce unwanted artifacts I associated with Suno-generated music. During development, I also clarified what the implementation actually does. This version is **a mastering helper using high-frequency attenuation, EQ, dynamics control, and loudness adjustment**, rather than a dedicated noise-isolation or audio-restoration system.

The code and interface artwork are bundled into one HTML file. Audio processing runs locally, and users can compare the original and processed versions before exporting a WAV file.

## Audio terms in plain language

| Term | What it means | What this application does |
| --- | --- | --- |
| **High-frequency softening** | Slightly reducing the upper frequency range to make sharp or piercing sounds less prominent. | The softening control lowers high-frequency content. This may reduce harshness, but too much can make a track sound dull. It does not selectively isolate and remove noise. |
| **EQ (equalization)** | Adjusting the levels of low, mid, and high frequencies separately to change the overall tone. | Low-frequency adjustments affect weight; mid-frequency adjustments affect the presence of vocals and instruments; high-frequency adjustments affect brightness and clarity. The actual result depends on the track. |
| **Dynamics control** | Adjusting the contrast between louder and quieter sounds in music. | The dynamics control uses compression to reduce louder sounds relative to quieter ones. This can make playback more even, but excessive compression can reduce liveliness or impact. |
| **Loudness adjustment** | Measuring how loud a whole track is and adjusting its level toward a selected target. | The application raises or lowers the track using LUFS as its loudness measure. Peak protection may keep the result below the target. A louder result does not necessarily mean better sound quality. |

For example, a quiet track with harsh highs could benefit from **softening the highs, shaping the tone with EQ, applying dynamics control if needed, and adjusting the overall loudness**. Not every track needs all four processes, especially at strong settings.

## Features

- **Local processing:** Audio is not uploaded to an external server.
- **Track analysis:** Integrated loudness in LUFS, sample peaks, and estimated True Peak.
- **Four sound styles:** Clean Guitar, Warm Vocal, Punch Drums, and Air Bass.
- **Manual controls:** Low/mid/high EQ, high-frequency softening, stereo width, and dynamics control.
- **Effect intensity:** Blend tonal and compression processing with the original from 0–100%. Loudness adjustment and peak protection remain separate.
- **Automatic recommendations:** Choose Natural, Soft, or Clear. Conservative settings and explanations are proposed using loudness, peak headroom, band-energy balance, and other measurements.
- **Apply and render:** Apply the proposed settings alone, or apply them and generate a master.
- **Original/master comparison:** Optional loudness matching and a shared loop region.
- **Comparison regions:** Drag the waveform, enter start/end times, or mark the current playback position.
- **Undo/redo:** Restore settings, including an automatic recommendation as a single undo step.
- **Peak protection:** Estimated True Peak using 4× FIR interpolation, a stereo-linked limiter, and post-processing verification.
- **Before/after summary:** Loudness, peaks, high-frequency balance, and the difference between peak and average level.
- **Settings storage:** Save and recall one configuration in the browser.
- **WAV export:** 16-bit or 24-bit PCM WAV.

## Getting started

1. Download `BEAT-Bandroom.html` and open it in Chrome or Edge.
2. Select an audio file and wait for the full-track analysis.
3. Use automatic recommendations or choose a style and adjust the controls manually.
4. Generate a master using the main render button or the automatic apply-and-render button.
5. Compare the original and master with playback loudness matched.
6. Export the result as WAV if you like it.

Changing settings requires regenerating the master. Loop regions and comparison loudness matching affect preview playback only. WAV export includes the complete track.

### Supported scope

- Mono or stereo audio
- WAV, MP3, and other formats the browser can decode
- Up to 150 MB per file; tracks of 10 minutes or less are recommended
- A single HTML file that can run without an internet connection

Long files require more processing time and memory. The browser may resample audio to its processing sample rate during decoding. Original files are not modified.

## Development and validation

The application was implemented with AI coding assistance and refined through browser testing. The results below document development checks, not certification or a guarantee for every audio file.

### Functional checks

The final automatic-recommendation version passed **45 checks**, covering areas including:

- Reference-signal loudness measurement and target adjustment
- Inter-sample peak detection and limiter peak protection
- Exact source preservation before loudness control at 0% effect intensity
- Comparison-region input and validation
- Settings changes, undo/redo, and compatibility with previously saved settings
- Automatic analysis without changing settings, application, rendering, and single-step undo
- 16/24-bit WAV generation and decoding
- Silence and invalid-file handling

### Real-track checks

**Tavern Rain**

Listen: [YouTube](https://youtu.be/kMuIO5EZWfI?si=LhqyX5A8S7zipIyI)

Original and processed files were compared during development. The full track was also processed with the automatic-recommendation version. A test using the Soft direction produced:

- Approximately -15.43 LUFS original → -14.00 LUFS processed
- Approximately -1.86 dBTP estimated output True Peak
- A peak-to-average difference of approximately 14.0 dB, largely preserved

These are application measurements. The user described an earlier processed version as slightly smoother than the original. This is an individual listening impression, not a universal result.

**Dreamy Celestial Healing**

Listen: [YouTube](https://youtu.be/5ac6fhs1AkY)

A user-generated output and its original were checked independently using **FFmpeg**.

| Measurement | Original MP3 | Processed WAV |
| --- | ---: | ---: |
| Integrated loudness | -15.5 LUFS | -14.0 LUFS |
| True Peak | Approx. -4.1 dBTP | Approx. -2.6 dBTP |
| Loudness range, LRA | 3.6 LU | 3.6 LU |
| Decoded duration | 179.96 s | 179.96 s |
| Samples at or above 0 dBFS | None | None |

The output was **24-bit, 48 kHz, stereo**, with the same sample count and no waveform timing shift. A check of one-second windows found no missing-audio sections. Most of the change was an approximately 1.53 dB level increase, with the original waveform and dynamics largely preserved.

This supports correct loudness adjustment and file export for that track. It does not validate every limiter behavior or prove a listening-quality improvement across all genres.

## Limitations

- High-frequency softening is EQ processing. The application does not isolate specific noises, separate stems, or reconstruct vocals.
- Automatic recommendations use measurement-based rules, not an AI model that recognizes genres, instruments, or vocals.
- True Peak is estimated using 4× interpolation. This is not a certified meter, and results may differ from professional measurement tools.
- Peak protection and limiting constraints can prevent the selected loudness target from being reached.
- The application cannot restore information already lost or undo severe distortion. Exporting an MP3 as 24-bit WAV does not recover missing information.
- Match playback loudness when comparing versions so that a louder result is not mistaken for better sound quality.
- Personal test tracks do not need to be included in the repository. Confirm the rights to any audio or artwork you use or distribute.

## Project scope

### Artwork

The character illustrations used in the application's UI were generated with Midjourney.

This is a small, AI-assisted audio project built to address a personal listening problem. It does not claim to replace professional mastering or audio restoration. It is not affiliated with or officially supported by Suno.

The goal is to reduce some unpleasant high-frequency character while preserving the music's original feel.

## License

This application and its accompanying documentation are distributed under the **[MIT License](LICENSE)**.

- Both personal and commercial use are permitted.
- You may use, copy, modify, redistribute, and sell copies of the software.
- Copies or substantial portions of the software must retain **H.Lambda's copyright notice and the MIT license notice**.
- The software is provided as is, without a warranty of suitability for a particular purpose or error-free operation. See `LICENSE` for the full warranty disclaimer and limitation of liability.

Copyright (c) 2026 H.Lambda

This license does not automatically apply to audio files loaded by users or to third-party materials added separately. Check the applicable terms for those materials.
