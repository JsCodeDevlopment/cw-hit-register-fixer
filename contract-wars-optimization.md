# Complete Optimization Guide (Regedit and System) — Contract Wars (`CWClient.exe`)

This document provides the **full step-by-step process for Windows Registry (`regedit`) and system tweaks**, combining advanced network/CPU optimizations recommended by the game moderation team with aim precision and smoothness adjustments for the **Unity 4.1.5f1 engine (32-bit / DirectX 9)** used by [CWClient.exe](file:///d:/Backup/CWClient/CWClient.exe).

---

## Part 1: Complete Step-by-Step Regedit Changes

> [!IMPORTANT]
> Before you start, open Registry Editor as Administrator: press `Win + R`, type `regedit`, press **Enter**, and approve the Administrator permission prompt.

### Step 1: Processing Priority for Games (`Tasks\Games`)

1. In `regedit`, navigate to the following path (you can copy and paste it into the Regedit address bar):
   ```text
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games
   ```
2. In the right panel, double-click each value below and set it as indicated:
   * **`Priority`**: Change the value data to **`6`** (Hexadecimal base).
   * **`GPU Priority`**: Make sure it is set to **`8`** (Hexadecimal base).
   * **`Scheduling Category`**: Change the text from `Medium` to **`High`**.
   * **`SFIO Priority`**: Change the text from `Normal` to **`High`**.

---

### Step 2: Disable Network Throttling and CPU Reservation (`SystemProfile`)

1. Still in `regedit`, go up two levels to the `SystemProfile` key:
   ```text
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile
   ```
2. In the right panel, locate and change these two values:
   * **`SystemResponsiveness`**: Double-click it, select **Hexadecimal** base, and set it to **`0`** (or `00000000`).
   * **`NetworkThrottlingIndex`**: Double-click it, select **Hexadecimal** base, and set it to **`ffffffff`** (eight "f" characters).

---

### Step 3: TCP/IP Packet and Hitreg Optimization (`Interfaces`)

> [!WARNING]
> **Attention before Step 3.1:** Deleting folders inside `Interfaces` makes Windows rebuild from scratch only your currently active network adapters that use **DHCP (Automatic IP)**. If you use a **manually configured static IP** in Windows, write down your IP/DNS settings before continuing.

#### 3.1. Cleanup of Old/Ghost Interfaces (Moderator Method)
1. Navigate to this key:
   ```text
   HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces
   ```
2. Expand the `Interfaces` folder. You will see multiple subfolders with coded names in `{GUID}` format (example: `{45184fbc-cbd4-43dc-...}`).
3. **Delete all `{GUID}` subfolders** inside `Interfaces` (right-click each `{GUID}` subfolder -> **Delete**).
4. **Restart your computer immediately.** After reboot, Windows will recreate only the folders for real, currently active network adapters.

#### 3.2. Create Instant Response Keys (Delay-Free Firing)
1. After restarting, open `regedit` again and return to:
   ```text
   HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces
   ```
2. Click through the few `{GUID}` folders that Windows recreated until you identify the one with **`DhcpIPAddress`** (or **`IPAddress`**) matching your local network IP (usually `192.168.x.x`).
3. Inside that active network adapter `{GUID}` folder, right-click on an **empty area** in the right panel -> **New** -> **DWORD (32-bit) Value**, and create these **3 files** below (respecting uppercase/lowercase letters):
   * **File 1:**
     * Name: **`TcpAckFrequency`**
     * Value: **`1`** (Hexadecimal base)
   * **File 2:**
     * Name: **`TCPNoDelay`**
     * Value: **`1`** (Hexadecimal base)
   * **File 3:**
     * Name: **`TcpDelAckTicks`**
     * Value: **`0`** (Hexadecimal base)

---

### Step 4: Remove Mouse Acceleration in Registry (`Control Panel\Mouse`)

1. In `regedit`, navigate to:
   ```text
   HKEY_CURRENT_USER\Control Panel\Mouse
   ```
2. Change the following values (Text / `REG_SZ`):
   * **`MouseSpeed`**: Set to **`0`**
   * **`MouseThreshold1`**: Set to **`0`**
   * **`MouseThreshold2`**: Set to **`0`**
   * **`MouseSensitivity`**: Make sure it is **`10`** (equivalent to the 6th central notch in Windows, 1:1 ratio).
3. *(Control Panel alternative)*: Press `Win + R`, type `main.cpl`, go to the **Pointer Options** tab, uncheck **"Enhance pointer precision"**, and keep pointer speed exactly at the **6th notch (middle)**.
4. **Restart your computer** so all Registry changes take effect.

---

### Step 5: Additional Tweaks (Game, Mouse, and GPU)

For Regedit changes to deliver full real-world results:
* **Mouse Polling Rate:** Lock it to **500Hz** or **1000Hz** in your mouse software (values above 1000Hz can cause stuttering in Unity 4's 32-bit engine).
* **Display Mode:** Always play in **Fullscreen** to avoid frame delay from the Windows Desktop Window Manager (DWM).
* **[CWClient.exe](file:///d:/Backup/CWClient/CWClient.exe) properties:** In the **Compatibility** tab, enable **"Disable fullscreen optimizations"** and **"Run this program as an administrator"**.
* **AMD Software (Adrenalin):** Enable **Radeon Anti-Lag** and keep **V-Sync** and **Enhanced Sync** disabled.

---

## Part 2: What Each Step Does and How It Affects Gameplay

Below is the detailed technical explanation of how each Windows tweak works internally and its direct impact in **Contract Wars**.

### 1. `Tasks\Games` Key (`Priority = 6`, `GPU Priority = 8`, `Scheduling Category = High`)
* **What it does in Windows:** Windows has a service called *MMCSS (Multimedia Class Scheduler Service)* that manages CPU execution queues. By default, `Priority` is set to `2` and the category to `Medium`. Raising `Priority` to `6` and `Scheduling Category` / `SFIO Priority` to `High` instructs the Windows kernel scheduler to place game threads at the top of the CPU and GPU execution queue.
* **Gameplay impact:**
  * Since [CWClient.exe](file:///d:/Backup/CWClient/CWClient.exe) runs on Unity 4.1.5 (which heavily depends on a single main CPU core to process physics, shooting, and rendering), any background interruption causes *micro-stuttering* (small freezes when many players shoot or throw grenades).
  * With `6` priority and `High` scheduling, minimum FPS becomes much more stable, and response time between mouse click and rendered frame (*frame pacing*) becomes consistent.

---

### 2. `SystemProfile` Key (`SystemResponsiveness = 0` and `NetworkThrottlingIndex = ffffffff`)

#### A) `SystemResponsiveness = 0`
* **What it does in Windows:** By default, Windows sets this value to `20` (decimal), meaning it forcibly reserves **20% of CPU capacity** for system background tasks (updates, indexing, open browsers). Changing it to `0` reduces this reservation to the kernel's minimum safety floor (10%), freeing **90% of real-time processing time** for the game.
* **Gameplay impact:** Removes sudden FPS drops caused by Windows background processes during matches.

#### B) `NetworkThrottlingIndex = ffffffff`
* **What it does in Windows:** Windows has a native limiter that restricts network packet processing to a maximum of **10 packets per millisecond** when multimedia apps/games are running (to save CPU for the sound card). Setting `ffffffff` (hexadecimal for `4294967295`) **fully disables this network throttling**.
* **Gameplay impact:**
  * During heavy firefights (many players shooting at the same time), the server sends dozens of packets per millisecond.
  * Without this tweak, Windows queues excess packets, creating ping spikes (*jitter*) and delayed enemy movement. With `ffffffff`, all match packets are processed instantly by the network adapter.

---

### 3. `Tcpip\Parameters\Interfaces` Key (Cleanup + TCP Triad)

#### A) Delete `{GUID}` folders and Restart
* **What it does in Windows:** Removes residual settings from old network adapters, uninstalled VPNs, and virtual tunnels that leave registry clutter, forcing the OS to detect and register only your current network adapter again.
* **Gameplay impact:** Ensures a clean network stack and prevents the classic error of applying TCP optimizations to an inactive or virtual adapter.

#### B) `TcpAckFrequency = 1`, `TCPNoDelay = 1`, and `TcpDelAckTicks = 0`
* **What it does in Windows:**
  * Unlike modern games that use only UDP, the older Unity 4 engine uses **TCP/IP** connections, which by default are affected by two Windows mechanisms designed to save bandwidth in file transfers: the **Nagle Algorithm** and **Delayed ACK**.
  * **`TCPNoDelay = 1`**: Disables the Nagle Algorithm, which normally holds small packets in the network adapter while waiting to bundle more data before sending.
  * **`TcpAckFrequency = 1`**: Forces Windows to acknowledge each received packet immediately (`1` for `1`) instead of waiting to accumulate `2` packets before replying to the server.
  * **`TcpDelAckTicks = 0`**: Sets the internal Windows *Delayed ACK* timer to zero (which by default can wait up to **200ms** before sending pending acknowledgements).
* **Gameplay impact:**
  * **This is the most important tweak for *Hitreg* (shot registration).**
  * It drastically reduces the *"noreg"* issue (when you shoot an enemy, see blood on your screen, but die without dealing damage because your last shot packets were held for a few milliseconds in the Windows buffer). Your shots are sent from the network adapter at the exact millisecond you click.

---

### 4. `Control Panel\Mouse` Key (`MouseSpeed = 0`, `MouseThreshold1/2 = 0`)
* **What it does in Windows:** Mathematically disables the Windows pointer acceleration curve.
* **Gameplay impact:**
  * [CWClient.exe](file:///d:/Backup/CWClient/CWClient.exe) does not have modern raw mouse input (*Raw Input*) and inherits movement directly from Windows.
  * With acceleration disabled (`0`), the distance you move your mouse on the pad always corresponds to **the same turning angle in-game**, regardless of how fast you flick (*flick shot*), making muscle memory 100% predictable.

---

### Quick Summary of Regedit Values

| Regedit Path | Value Name | Type | Recommended Value | Main In-Game Effect |
| :--- | :--- | :--- | :--- | :--- |
| `...\Multimedia\SystemProfile\Tasks\Games` | `Priority` | `REG_DWORD` | `6` | High CPU priority for the game |
| `...\Multimedia\SystemProfile\Tasks\Games` | `GPU Priority` | `REG_DWORD` | `8` | Maximum graphics (GPU) priority |
| `...\Multimedia\SystemProfile\Tasks\Games` | `Scheduling Category` | `REG_SZ` | `High` | Faster kernel scheduling queue |
| `...\Multimedia\SystemProfile\Tasks\Games` | `SFIO Priority` | `REG_SZ` | `High` | High read/input (I/O) priority |
| `...\Multimedia\SystemProfile` | `SystemResponsiveness` | `REG_DWORD` | `0` | Dedicates up to 90% real-time CPU to the game |
| `...\Multimedia\SystemProfile` | `NetworkThrottlingIndex` | `REG_DWORD` | `ffffffff` | Removes network packets-per-ms limit |
| `...\Tcpip\Parameters\Interfaces\{GUID}` | `TcpAckFrequency` | `REG_DWORD` | `1` | Acknowledges each packet immediately |
| `...\Tcpip\Parameters\Interfaces\{GUID}` | `TCPNoDelay` | `REG_DWORD` | `1` | Disables Nagle Algorithm (immediate send) |
| `...\Tcpip\Parameters\Interfaces\{GUID}` | `TcpDelAckTicks` | `REG_DWORD` | `0` | Removes acknowledgement delay (Delayed ACK) |
| `HKCU\Control Panel\Mouse` | `MouseSpeed` | `REG_SZ` | `0` | Removes mouse acceleration (1:1 aim) |
