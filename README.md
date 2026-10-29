# Contract Wars Hit Register Fixer

## Gameplay and Performance Improvements

This document describes **what is improved in practice** and **which aspects of gameplay** benefit from the system and network optimizations in **Contract Wars**.

---

## 🎯 Overview of the Impact on the Game

Because it runs on an old 32-bit engine (Unity 4 / DirectX 9) and connects to physically distant servers, Contract Wars naturally suffers from communication delays, frame-time fluctuations, and imprecise mouse input caused by Windows' default behavior.

These optimizations remove those operating-system limitations to provide **immediate command response**, **accurate shot registration**, and **visual stability** throughout the match.

---

## 🚀 Gameplay Aspects That Are Improved

### 1. Shot Registration (*Hitreg*) and Elimination of *Nereg*

* **What improves:** Communication between your click and the server is no longer held back or delayed before transmission, allowing every fired bullet to be sent and confirmed immediately.
* **Practical impact during matches:**
  * **End of “ghost” shots (*nereg*):** Situations where you shoot an opponent, see blood on screen, but the server does not register the damage are drastically reduced.
  * **Lower *Time-to-Kill* (TTK):** With automatic weapons (assault rifles and submachine guns), every bullet in a burst is registered in sequence, requiring less ammunition to eliminate an enemy.
  * **Advantage for single shots (snipers and shotguns):** The first shot reaches the server without residual delay, preventing you from dying after firing first.

---

### 2. Stability During Intense Firefights and Reduced *Desync*

* **What improves:** The operating system no longer limits the amount of network information processed simultaneously while the game is running.
* **Practical impact during matches:**
  * **Fights with multiple players:** When several players are shooting at once, grenades are exploding, and visual effects fill the screen, the connection avoids stutters and sudden latency spikes.
  * **More accurate enemy movement:** The effect of enemies “teleporting” or sliding at abnormal speeds while strafing or peeking is reduced.
  * **Fewer deaths behind cover:** Reduced synchronization delay (*desync*) helps prevent taking damage after you have already moved behind a wall.

---

### 3. Smoother Frames (FPS) and the End of *Micro-Stuttering*

* **What improves:** The processor and graphics card give the game maximum priority, preventing background Windows tasks from taking performance away during a match.
* **Practical impact during matches:**
  * **Clean aim-down-sights (ADS/scope):** Opening a scope or holographic sight does not cause a small stutter at the exact moment you are about to shoot.
  * **Smooth quick turns:** Rapidly turning the camera to react to an enemy behind you keeps the image continuous and fluid.
  * **Stable minimum FPS:** Even in the heaviest map areas or during smoke and explosions, the frame rate remains consistent.

---

### 4. 1:1 Aim Precision and Consistent Muscle Memory

* **What improves:** In-game aim movement responds in a fully linear and proportional way to your physical mouse movement, without interference from system acceleration curves.
* **Practical impact during matches:**
  * **No more “slippery” or “heavy” aim:** The crosshair does not overshoot the target during a fast flick shot or become too slow during precise long-range adjustments.
  * **Predictable recoil control (*spray control*):** Because sensitivity remains constant, compensating for weapon recoil becomes easier and more natural.
  * **Better headshot and no-scope alignment:** It becomes easier to keep the crosshair at head height (*pre-aim*) and make quick shots without aiming (*hipfire* / *no-scope*) with greater consistency.

---

### 5. Reduced Command Delay (*Input Lag*)

* **What improves:** The total time between a physical mouse or keyboard action and the moment that movement is rendered on the monitor is reduced.
* **Practical impact during matches:**
  * Immediate control and a crosshair that feels “connected” to your hand.
  * Faster reaction time in close-quarters encounters, where a few milliseconds can decide who wins the firefight.

---

## 📊 Summary of Gameplay Improvements

| Gameplay Aspect | Default Behavior (Without Optimization) | Optimized Behavior |
| :--- | :--- | :--- |
| **Damage Registration (*Hitreg*)** | Delayed shot confirmation and frequent *nereg* | Immediate confirmation of every shot and consistent damage |
| **Firefights with Multiple Players** | Inconsistent response and delayed packet processing | Stable connection even under intense gunfire and explosions |
| **Visual Stability (FPS)** | Small stutters when aiming or turning quickly | Continuous frames and consistent smoothness during combat |
| **Aim Response** | Variable speed that hurts muscle memory | 1:1 precision faithful to real hand movement |
| **Reaction Time (*Input Lag*)** | Heavy feeling or slight delay in screen movement | Immediate visual response to mouse and keyboard input |
