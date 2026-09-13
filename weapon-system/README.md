# 🔫 Data-Driven Weapon Audio System & Dual-Architectural Implementation

## 🌐 Overview
A comprehensive weapon audio implementation in Unreal Engine 4 and Audiokinetic Wwise demonstrating two radically different architectural approaches to gun sound design: a **Modular (State-Driven)** pipeline for rapid automatic fire, and a **Monolithic (Asset Consolidation)** pipeline for heavy single shots. Driven by a scalable Data-Driven architecture, the system includes 3D spatialization, distance attenuation, frequency-filtering RTPCs, and robust Blueprint state checks.

## 🛠️ Tools & Tech Stack
- **Game Engine:** Unreal Engine 4.27
- **Audio Middleware:** Audiokinetic Wwise 2021.1.14
- **Recording & Audio Specs:** WAV format (48 kHz, 16/24-bit); Mono for positional shots/mechanics; Stereo for tails and echoes to build spatial depth. Peak volumes normalized between **-3 dB and -1 dB** with tight transient trimming and zero micro-gaps.

---

## 🎨 Design Philosophy: Modular vs. Monolithic

1. **Rifle — Modular / State-Driven Approach:**
   - **Problem:** Rapid automatic fire causes audio clutter, voice overlap, and unnatural "muddy" tails when playing full shot assets continuously.
   - **Solution:** Shot mechanics are split into 3 micro-events: *Projectile Body*, *Bullet Tail/Echo*, and *Shell Casing Ejection*.
   - **Logic:** Releasing the Left Mouse Button (LMB) sends a `Stop Firing` command that cleanly triggers the tail/echo and shell ejection only on the final shot of a burst.

2. **Shotgun — Monolithic / Asset Consolidation:**
   - **Problem:** Creating complex multi-node event structures for slow-firing weapons adds unnecessary CPU overhead and Blueprint complexity.
   - **Solution:** A single, professionally mixed, consolidated sample (shot + tail + pump + casing) recorded in a single acoustic environment.
   - **Optimization:** Fires once every 1–2 seconds. Saves CPU cycles, avoids unnecessary Blueprint nodes, and eliminates extra voice concurrency in Wwise while maintaining maximum audio richness.

---

## ⚙️ Wwise Architecture, Spatialization & Attenuation

- **Hierarchy Structure:** Separated into three distinct folders: `Rifle`, `Shotgun`, and `Shared_gear` (handling equip, reload, and empty magazine clicks).
- **Anti-Repetition & Pitch Variation:** Random Containers utilize anti-repetition algorithms (preventing back-to-back duplicate triggers) combined with real-time pitch randomization to prevent phase artifacts/flanger effects during automatic fire.
- **Distance Attenuation Curves (50m Radius / 40m Operational + 10m Camera Buffer):**
  - *Camera-Centric Listening:* Sound perception is anchored to the player camera rather than the character mesh. A 10-meter technical buffer prevents audio dropouts when the camera pulls back.
  - *Main Shot Attenuation:* 50m max radius with a sharp drop-off after 35m.
  - *Tail/Echo Attenuation:* Uses a flatter retention curve allowing gunshot tails to remain audible over greater distances.
  - *Shell Casing Attenuation:* Tightly clamped to an **8m radius** so lightweight shell drops do not clutter distant spatial mixes.
- **Spectral Distance Filtering (RTPC):** Low-Pass Filter (LPF) curve on the Actor-Mixer cuts high frequencies over distance, simulating real-world atmospheric absorption.

---

## 🎮 Unreal Engine 4 Implementation Logic

- **Data-Driven Architecture:** All weapon parameters are decoupled from character logic and stored in clean Data Assets (`DA_Rifle`, `DA_Shotgun`) and Structures. Weapon logic is encapsulated within a universal `BP_WeaponComponent` and `BP_WeaponActor`.
- **Equip & Swap Systems (`E` & `Q` Keys):**
  - Spawns/attaches weapon meshes via custom socket points.
  - Features strict safety checks (`isEquipping`, `isArmed`) that block firing during weapon transitions or unarmed states.
- **Universal `Shoot` Function:**
  - Evaluates firing conditions via a `canShoot` validation node (checking ammo, weapon status, reloading, and shotgun-specific cooldowns).
  - Handles trace calculation, damage application, ammo consumption, empty magazine sound triggers, and auto-reloading when magazines reach zero.
- **Enemy Actor Testing Setup:** An external Enemy Actor periodically fires automatic bursts and shotgun shots every 3 seconds to validate 3D positioning, LPF distance rolls, and spatial attenuation in real-time.

---

## 🎬 Result & Profiling
- Verified using the Wwise Profiler: Voice limit constraints operate flawlessly with zero memory leaks, no "phantom" lingering background voices, and clean separation between modular rifle bursts and monolithic shotgun impacts.
- Perfectly balanced spatial mix with clear distance cues, distinct casings, and responsive UI/gameplay feedback.
