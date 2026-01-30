# Optimized Potato PCVR for VRChat (CachyOS + Flatpak + WiVRN)

This repository provides an aggressive optimization guide for running VRChat on extremely low-end systems, integrated GPUs (iGPU), or "Potato PCs." It utilizes **CachyOS**, **Flatpak Steam**, and **WiVRN** for the most efficient VR streaming possible.

## 1. Prerequisites & Dependencies

To ensure all launch options (like `jemalloc` and `VA-API`) function correctly, you must install the following on your host system and within Flatpak.

### Host System (CachyOS)
```bash
# Install core utilities and hardware video acceleration drivers
sudo pacman -S jemalloc gamemode lib32-gamemode gamescope \
libva-mesa-driver libva-intel-driver intel-media-driver
```

```bash
# Install Steam and its utility extensions
flatpak install flathub com.valvesoftware.Steam \
com.valvesoftware.Steam.Utility.gamescope \
com.valvesoftware.Steam.CompatibilityTool.Proton-GE \
org.freedesktop.Platform.VAAPI.Mesa
```

## 2. Compatibility Tool (Proton GE)

VRChat requires **Proton GE** to fix in-game video players (AVPro) and improve overall performance on Linux.

### Setup Steps:
1. **Open Steam.**
2. **Navigate to VRChat:** Right-click **VRChat** in your library and select **Properties**.
3. **Compatibility Tab:** Go to the **Compatibility** section.
4. **Enable Override:** Check the box: `"Force the use of a specific Steam Play compatibility tool."`
5. **Select Version:** Choose **GE-Proton (Flatpak)** from the dropdown list.

> [!TIP]
> If you don't see GE-Proton in the list, make sure you have installed the `com.valvesoftware.Steam.CompatibilityTool.Proton-GE` Flatpak extension mentioned in the Prerequisites.

## 3. Steam Launch Options

Copy and paste the following command into **VRChat** > **Properties** > **General** > **Launch Options**:

```Steam Launch Options(VRChat)
MALLOC_CONF="background_thread:true,metadata_thp:always,dirty_decay_ms:500" DXVK_STATE_CACHE=1 gamemoderun flatpak-spawn --host gamescope -W 1280 -H 720 -w 1280 -h 720 -f -r 60 -- PRESSURE_VESSEL_IMPORT_OPENXR_1_RUNTIMES=1 %command% --enable-avpro-in-proton --enable-hw-video-decoding -force-d3d11-singlethreaded -screen-width 320 -screen-height 240 -nolog -no-analytics
```

## 4. Crucial In-Game & App Settings

For Potato PCVR, software-side optimization is just as important as the launch commands.

### VRChat In-Game Settings (Graphics)
Adjust these settings within the VRChat Main Menu under **Settings** > **Graphics**:

- **Quality Preset**: `Custom`
- **Anti-Aliasing**: **`Disabled`** (Crucial: AA is extremely heavy for iGPU resources)
- **Shadows**: `Low`
- **Level of Detail**: `Low`

### WiVRN (Quest App) Optimization
Configure these settings within the **WiVRN menu** on your headset to balance performance and clarity:

| Tab | Setting | Recommended Value |
| :--- | :--- | :--- |
| **Settings** | Refresh Rate | `60` |
| **Settings** | Render Resolution | `50% (~916x1007 per eye)` |
| **Settings** | Bitrate | `25Mbit/s` |
| **Settings** | High Power Mode | `Enabled` |
| **Post-processing** | Supersampling | `Quality` |
| **Post-processing** | Sharpening | `Quality` |

> [!NOTE]
> By setting **Anti-Aliasing to Disabled** in VRChat and using **Sharpening** in WiVRN, you can maintain readable text while significantly reducing the load on your integrated GPU.

## 5. Why these settings?

Optimizing for a "Potato PC" requires shifting the workload from limited resources (like VRAM and CPU) to dedicated hardware engines or more efficient algorithms.

* **Anti-Aliasing: Disabled**
    * Anti-Aliasing (AA) is extremely computationally expensive for integrated graphics. Disabling it provides a massive FPS boost by skipping complex edge-smoothing passes.
* **WiVRN 50% Res + Sharpening**
    * By rendering at half resolution and applying a **Sharpening filter** via WiVRN, we drastically reduce the VRAM footprint while maintaining enough clarity to read in-game text.
* **jemalloc (`MALLOC_CONF`)**
    * Linux's default memory allocator can be inefficient with VRChat's memory spikes. Using `jemalloc` with these flags stabilizes memory allocation, preventing "Out of Memory" crashes common on 8GB/16GB shared-memory systems.
* **Hardware Decoding (`--enable-hw-video-decoding`)**
    * This offloads video playback (e.g., Movie Worlds) to the iGPU's dedicated **Fixed-Function Media Engine**. This prevents massive CPU spikes and stuttering when someone joins a world with a video player.



---

## Disclaimer

This configuration is **"Performance First."** To make VR playable on hardware that was never intended for it, you should expect visual artifacts, shimmering, and lower graphical fidelity. Use this as a baseline and adjust as your hardware allows.
