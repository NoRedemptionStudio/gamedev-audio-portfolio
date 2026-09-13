
# 🎵 Dynamic Combat Music & Project-Wide Audio Mixing Architecture

## 🌐 Overview
An adaptive, multi-state interactive music system built in Unreal Engine 4 and Audiokinetic Wwise, paired with a project-wide final audio mix and optimization pass. The system seamlessly transitions between exploration, tension, and combat states based on player proximity, line-of-sight, and enemy aggro counts, while employing strict anti-flapping logic and Wwise resource management.

## 🛠️ Tools & Tech Stack
- **Game Engine:** Unreal Engine 4.27
- **Audio Middleware:** Audiokinetic Wwise 2021.1.14
- **Music Specs:** 110 BPM, 4/4 Time Signature. Custom composition structured into 7 stems (4 state loops + 3 dedicated transition segments) with embedded tails for seamless crossfading.

---

## 🎼 Music States & Behavioral Psychology Matrix

### 1. Game States
- **`Explore`:** Peaceful environmental exploration.
- **`Tension`:** Nearby threat alert (triggered within 25m radius).
- **`Combat_Low`:** Engagement with isolated/small groups of enemies.
- **`Combat_High`:** High-intensity battles against large enemy clusters.

### 2. Transition Rules & Player Psychology
- **Immediate Combat Engagement (`Explore`/`Tension` -> `Combat_Low`):** Exit source set to `Immediate` to instantly signal danger and demand immediate player action.
- **Smooth Musical Transitions (`Explore` <-> `Tension`):** Exit source set to `Next Bar` with `Post-Exit` tail playback and subtle fade-outs for fluid atmospheric shifts.
- **Progressive Build-up (`Combat_Low` -> `Combat_High`):** Exit source set to `Exit Cue` to dynamically escalate tension without jarring structural jump-cuts.
- **"Sticky Adrenaline" Rule:** To preserve combat momentum, neutralising individual enemies does *not* downscale music from `Combat_High` to `Combat_Low`. The system maintains maximum musical intensity until the last enemy is defeated or the player flees, preventing artificial "relaxed" beats mid-battle.

---

## 🎮 Unreal Engine 4 Logic & Anti-Flapping Architecture

- **Enemy Blueprint Sensing:**
  - **Proximity Sphere (25m):** Triggers `Tension` state upon player entry.
  - **Line-of-Sight Detection:** Replaced standard Pawn Sensing with custom **Line Trace** logic for precision aggro checking.
- **Two-Layer Anti-Flapping System:**
  - **Layer 1 (Enemy Local Cooldown):** Breaking line-of-sight triggers a **7-second delay timer** before lowering the threat state, preventing musical stutter if a player momentarily steps behind cover.
  - **Layer 2 (`MusicManager_BP`):** A centralized manager aggregates total aggro and proximity counts across all enemy actors on the map. Evaluated via the `updateMusicState` function, it prevents the music from dropping back to `Explore` if the player breaks line-of-sight with one enemy while remaining engaged with others.

---

## 🎛️ Project-Wide Master Mixing & Auto-Ducking

### 1. Bus Architecture
- **`Master Audio Bus`**
  - **`Music Bus`:** Controls interactive score volume. Equipped with a **Wwise Peak Limiter (-1 dB threshold)** to prevent volume spikes during overlap/crossfade points between state transitions.
  - **`Environment Bus`:** Controls 2D/3D ambient soundscapes. Bound to game states to automatically attenuate background ambiences during high-tension combat.
  - **`SFX Bus`:** Controls critical gameplay entities (Footsteps and Weapon audio).

### 2. Priority & Auto-Ducking Strategy
- **Bus Priorities:** `Music` (Highest / Continuous) > `Weapons` (Loud Key Events) > `Footsteps/Movement` (Contextual) > `Environment` (Background).
- **SFX Auto-Ducking:** The `SFX Bus` automatically ducks the `Music Bus` during active gunfire or footstep events, guaranteeing that critical tactical feedback remains crisp and audible without drowning the mix.

---

## ⚡ Voice Limits & SoundBank Optimization

### 1. Playback Limits & Virtual Voice Policies
- **Footsteps (`AM_Footsteps_Master`):**
  - *Max Instances:* **6** (prevents phase interference from multiple running NPCs).
  - *Virtual Voice:* `Kill Voice` (short 0.2s–0.3s transient samples are instantly destroyed to free memory).
- **Weapons (`AM_Weapons`):**
  - *Max Instances:* **12** (preserves layered shots, casings, and tail reverberation during automatic fire).
  - *Virtual Voice:* `Use Virtual Voice / Kill` (preserves long distant reverberation tails without clogging foreground channels).
- **Environment (`AM_Environment`):**
  - *Max Instances:* **4** (ideal background density per emitter object).
  - *Virtual Voice:* `Continue to Play` (preserves timeline positioning for long looping ambient tracks when leaving and re-entering rooms).

### 2. Codec & Conversion Settings (`Vorbis`)
- **`CS_Music`:** Vorbis compression set to **Quality 4** (delivers pristine musical fidelity with low memory overhead).
- **`CS_SFX`:** Vorbis compression set to **Quality 2** (drastically reduces RAM footprint for hundreds of short SFX files without audible quality loss).

---

## 🎬 Result
A highly adaptive, polished interactive music experience backed by an optimized, professional game-ready audio pipeline. Zero voice leaks, zero state flapping, and an intelligent mix that balances music impact with gameplay-critical sound effects.
