# 👟 Surface-Based Physical Material Footstep System

## 🌐 Overview
A dynamic, automated footstep audio system built in Unreal Engine 4 and Audiokinetic Wwise. Designed with strict adherence to pipeline standards, clean blueprint hierarchies, and optimized memory management. The system detects physical surfaces in real-time, determines player movement states (Walk, Sneak, Sprint, Jump, Land), and triggers multi-layered audio events without hardcoding sounds into individual animations.

## 🛠️ Tools & Tech Stack
- **Game Engine:** Unreal Engine 4.27
- **Audio Middleware:** Audiokinetic Wwise 2021.1.14
- **Recording Hardware & Format:** Audio-Technica AT2020 microphone; WAV format (48 kHz, 16/24-bit, Mono/Stereo, cleaned of artifacts/clipping with normalized volume).

---

## 🎨 Sound Design & Asset Preparation
- **Library Creation:** Custom-recorded and processed independently to ensure absolute control over transients and weight.
- **Frequency Layering:** Footstep sounds are split into two frequency layers (sharp transient + body weight) to guarantee high intelligibility against loud ambient backgrounds without boosting overall volume.
- **Asset Volume Breakdown (Total: 44 sounds):**
  - **Run / Walk:** 5 sounds per surface.
  - **Jump (Take-off):** 3 sounds per surface.
  - **Land:** 3 sounds per surface.
  - **Sneak:** 3 sounds per surface.
- **Supported Surfaces (4 types):**
  1. `Concrete`
  2. `Wood`
  3. `Grass`
  4. `Gravel`

---

## ⚙️ Wwise Architecture & Optimization
1. **Switch Containers Hierarchy:** 
   - Organized into one main switch container for footsteps (sub-divided by surface, then by stealth/walking modes via Game Syncs) and two separate switch containers for jumping (take-off vs. landing to accommodate varying air-time heights).
2. **Dynamic Control via RTPC:** 
   - Instead of duplicating sound banks for running and sprinting, performance overhead is minimized by shifting dynamics handling entirely to `RTPC_CharacterSpeed`.
   - *Volume scaling:* Sneak = **25%** | Walk = **58%** | Sprint = **100%** (via volume and filtering curves). This approach saves RAM and simplifies the container structure.
3. **Randomization & Anti-Repetition:** 
   - Sounds are placed in Random Containers equipped with previous-item protection and pitch variation for instant perceptual variety.
4. **Events:** Three core sound-playback events directly linked to Wwise switch containers.

---

## 🎮 Unreal Engine 4 Implementation Logic
- **State Management (`ThirdPersonBP`):** 
  - Manages custom states (*sneak*, *crouch*, *sprinting*) alongside default idle/run states. Features hybrid trigger logic (Left Ctrl for toggleable sneak stance, Shift for dynamic sprinting).
  - *State Transition Polish:* Pressing Left Shift while in *sneak* mode instantly cancels sneak and transitions into a sprint (no need to toggle Ctrl off first). Releasing Shift smoothly drops the character back to normal walk mode.
- **Speed & Volume Parameters:**
  - Default speed: `350`
  - Shift pressed: Speed increases to `600`, volume scales up by `42%`.
  - Left Ctrl pressed: Speed drops to `200`, movement animation updates, and Wwise switches to *sneak* containers.
- **Surface Detection & Trigger Pipeline:**
  - Triggered via **Anim Notifies** on movement animations when the foot touches the ground.
  - Utilizes a custom `OnFootstep` event and **Line Trace by Channel** originating from designated skeletal mesh bones.
  - The ray reads the physical material and passes data to a `Set Switch` node before posting the Wwise event.
- **Safeguards & Landings:**
  - `lastPhysicalmaterial` variable acts as a fallback against blind spots and missed rays, providing context for jump mechanics.
  - For landings, an alternative branch bypasses cached states to freshly re-evaluate the surface via ray trace (e.g., jumping from a cliff into water or onto grass).

---

## 🎬 Result & Performance
- Clean, modular, and scalable architecture with zero memory leaks, perfect voice limit execution, and real-time responsiveness.
- Widgets on screen track active surface types, character speeds, and real-time volume percentage changes during gameplay testing.
