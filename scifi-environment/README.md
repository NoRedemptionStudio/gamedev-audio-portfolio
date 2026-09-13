# 🚀 Sci-Fi Environment — Dynamic Spatial Audio & State Systems

## 🌐 Overview
An interactive sci-fi facility audio system built in Unreal Engine 4 and Audiokinetic Wwise. Demonstrates advanced spatial audio propagation (Rooms & Portals, geometry-driven diffraction/transmission), dynamic environment state switches (`Normal` vs. `Alarm`), and hybrid deployment of audio emitters (Direct `AkComponent` placement vs. modular Blueprint encapsulation).

## 🛠️ Tools & Tech Stack
- **Game Engine:** Unreal Engine 4.27
- **Audio Middleware:** Audiokinetic Wwise 2021.1.14
- **Audio Crafting:** Field recordings, analog/sub-bass synthesis, AI voice generation with custom DSP post-processing. All loops seamlessly edited with zero zero-crossing pops or phase issues.

---

## 🎨 Creative Sound Synthesis & Layering

To build an organic yet alien industrial atmosphere, sound sources were crafted from unconventional real-world layers and synthesis:
- **2D Base Ambience:** Industrial space recordings + car A/C hum + sub-bass synth layers for low-end body.
- **Terminal Consoles:** Legacy computer fan noise + UI system clicks (8m Attenuation).
- **Ventilation Systems:** Pencil friction on cardboard + desktop fan + A/C units (15m Attenuation).
- **Power Reactor:** Kitchen oven hum + microwave transients + sub-frequency electromagnetic hum (20m Attenuation).
- **Facility Speakers / Siren:** Classic alarm siren blended with AI-synthesized, heavily processed emergency voice lines (15m Attenuation).
- **Blast Doors:** Layered mechanical latches and servos stored in Random Containers (`RC_door`) with dual events (`play_door_open`, `play_door_close`) for asset expansion scalability (10m Attenuation).

---

## ⚙️ Wwise Architecture & Spatial Audio

1. **Reverb & Room Acoustics:**
   - Configured **2 Auxiliary Buses** with `Wwise RoomVerb` (Big Room vs. Small Room).
   - Applied across **6 facility rooms** in UE4 by dynamically tuning send levels per room, creating unique acoustic footprints without overloading Wwise with redundant Auxiliary Buses.
2. **State-Driven Ambient Optimization (`play_console_init`):**
   - **Problem:** Constantly posting and stopping separate looping events during state switches creates voice overhead and risk of unstopped phantom loops.
   - **Solution:** A single initialization event (`play_console_init`) continuously plays both `console_loop` and `console_alarm`. Volume transitions are bound directly to Wwise **States** (`Normal` vs. `Alarm`), achieving instant, zero-latency state crossfades without triggering new audio events.
3. **Geometry, Transmission & Diffraction:**
   - All 3D emitters have **Obstruction/Occlusion, Transmission, and Diffraction** enabled, forcing audio to wrap around doorways and filter through walls based on UE4 spatial geometry rather than passing in a straight line.

---

## 🎮 Unreal Engine 4 Blueprint & Portal Logic

- **Deployment Strategy:**
  - *Static Ambient Emitters (e.g., Reactor):* Configured directly via individual `AkComponent` instances on the map with `Auto Post` enabled for zero Blueprint overhead.
  - *Interactive / Reusable Emitters (Vents, Consoles, Doors, Speakers):* Encapsulated within modular Blueprints (`Speaker_BP`, `Console_BP`, `Door_BP`).
- **Facility State Machine (`F` Key Console Interaction):**
  - Player collision with the console triggers `Console_BP`. Pressing `F` toggles the global facility state (`Normal` <-> `Alarm`).
  - *Alarm Trigger Sequence:* Button SFX -> Global State set to `Alarm` -> Console switches audio loop -> Signal sent to `Speaker_BP` to activate looping emergency protocols.
  - *Deactivation Sequence:* Button SFX -> `console_reboot` one-shot -> Global State set to `Normal` -> Console reverts loop -> Speaker plays "Alarm Cleared" voice notice.
- **Portals & Door Animation Synchronization:**
  - *Opening Sequence:* **Portal Opens -> Play Open Sound**. (Ensures spatial audio propagates immediately as the doorway clears).
  - *Closing Sequence:* **Play Close Sound -> Portal Closes**. (Prevents premature audio truncation/clipping of room audio before the physical door animation finishes closing).
- **Level Initialization:** `Level Blueprint` handles initial SoundBank loading and fires the global 2D ambient track on level load.

---

## 🎬 Result & System Performance
- Perfectly smooth spatial transitions across all 6 rooms with seamless portal sound propagation.
- Zero audio truncation during door closures and zero lingering voices during complex facility alarm toggles.
- Low memory footprint achieved by reusing auxiliary reverb send levels and state-driven volume blending.
