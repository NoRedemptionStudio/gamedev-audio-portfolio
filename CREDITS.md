# 📜 Credits & Audio Resources

This document acknowledges all digital audio workstations (DAWs), hardware, libraries, and specific third-party sound elements used throughout the audio design, synthesis, and middleware implementation of this project.

---

## 🛠️ DAWs, Hardware & Processing Tools
- **DAWs & Audio Editors:**
  - **Cockos REAPER** & **Image-Line FL Studio** — Used for synthesis, multi-track audio layering, transient sculpting, spatial processing, pitch modulation, and final mastering.
- **Hardware & Field Recording:**
  - **Audio-Technica AT2020** — Studio condenser microphone used to capture original physical Foley, room acoustics, and footstep layers.
- **AI Voice Generation:**
  - **ElevenLabs** — AI voice synthesis used to generate facility emergency alert lines and public address system announcements (further post-processed with custom DSP spatial reverb, distortion, and bandwidth filters in Wwise/DAW).

---

## 🎧 Sound Libraries & Third-Party Samples

All external audio materials were utilized exclusively as foundational sound design layers/textures. They were heavily re-processed (layered, pitch-shifted, filtered, and synthesized) and compiled directly into Wwise SoundBanks.

### 1. Freesound.org
- **Platform:** [Freesound.org](https://freesound.org)
- **Attributed Asset:** `SCIDoor_Spaceship Compartment Door.Different Variations x8_EM` by **newlocknew** (used under Creative Commons / Attribution License).
- **Implementation:** Processed and layered into the modular mechanical blast door opening/closing sound events (`RC_door`).

### 2. Pixabay Audio
- **Platform:** [Pixabay](https://pixabay.com)
- **Usage:** Raw intruder SFX, physical impact layers, and environmental textures (Royalty-Free Content License for integrated media/game implementation).

### 3. SoundDino
- **Platform:** [SoundDino.com](https://sounddino.com)
- **Usage:** Raw intruder and combat Foley elements, atmospheric drone textures, and mechanical transients (Royalty-Free for game builds).

---

## ⚖️ Legal & License Compliance Statement
- **Non-Commercial Portfolio Use:** All assets within this project are assembled strictly for non-commercial portfolio presentation and technical demonstration purposes.
- **No Standalone Redistribution:** Audio files are compiled and baked into optimized Wwise SoundBanks and Unreal Engine binary formats. Third-party raw audio files are **not distributed as standalone, uncompressed stock samples**, fully adhering to the redistribution terms of Pixabay, SoundDino, Freesound, and respective license holders.
