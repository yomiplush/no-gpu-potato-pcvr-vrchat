# Optimized Potato PCVR for VRChat (Flatpak + WiVRN)

This repository provides an aggressive optimization guide for running VRChat on extremely low-end systems, integrated GPUs (iGPU), or **"Potato PCs."** While **CachyOS** is the recommended distribution for maximum performance, this guide covers all major Linux environments.

---

## 1. Prerequisites & Dependencies

To ensure all optimizations (like `jemalloc` and `VA-API` hardware decoding) function correctly, you must install the following on your host system based on your distribution and hardware.

### Step 1: Install Host Utilities (By Distribution)

Arch Linux / CachyOS / EndeavourOS

```bash
sudo pacman -S jemalloc gamemode lib32-gamemode gamescope flatpak
```

Ubuntu/Debian

```bash
sudo apt update && sudo apt install libjemalloc2 gamemode gamescope flatpak
```

Fedora

```
sudo dnf install jemalloc gamemode gamescope flatpak
```

Step 2: Install Graphics Drivers (By Hardware)
Required for hardware video decoding (--enable-hw-video-decoding) on iGPUs.

Intel (Integrated Graphics / Arc)
Arch Linux:

```Bash
sudo pacman -S intel-media-driver libva-intel-driver
```

Ubuntu:

```Bash
sudo apt install intel-media-va-driver-non-free
```
Fedora:

```Bash
sudo dnf install intel-media-driver
```

AMD (Radeon / Ryzen iGPU)
Arch Linux:

```Bash
sudo pacman -S libva-mesa-driver mesa-vdpau
```
Ubuntu:

```Bash
sudo apt install mesa-va-drivers mesa-vdpau-drivers
```

Fedora:

```Bash
sudo dnf install mesa-va-drivers
```

# Install Steam and required utility extensions
```Bash
flatpak install flathub com.valvesoftware.Steam \
com.valvesoftware.Steam.Utility.gamescope \
com.valvesoftware.Steam.CompatibilityTool.Proton-GE \
org.freedesktop.Platform.VAAPI.Mesa
```

2. Compatibility Tool (Proton GE)
VRChat requires Proton GE for functional in-game video players (AVPro) and essential performance fixes.

Open Steam.

Navigate to VRChat: Right-click VRChat in your library and select Properties.

Compatibility Tab: Go to the Compatibility section.

Enable Override: Check the box: "Force the use of a specific Steam Play compatibility tool."

Select Version: Choose GE-Proton (Flatpak) from the dropdown list.

## 4. Crucial In-Game & App Settings

For Potato PCVR, software-side optimization is just as critical as the launch commands.

### VRChat Settings (Graphics)
Adjust these within the VRChat Main Menu under **Settings** > **Graphics**:

- **Quality Preset**: `Custom`
- **Anti-Aliasing**: **`Disabled`** (Crucial: AA is extremely heavy for iGPU resources)
- **Shadows**: `Low`
- **Level of Detail (LOD)**: `Low`

### WiVRN (Quest App) Settings
Configure these settings within the **WiVRN menu** on your headset:

| Tab | Setting | Recommended Value |
| :--- | :--- | :--- |
| **Settings** | Refresh Rate | `60` |
| **Settings** | Render Resolution | `50% (~916x1007 per eye)` |
| **Settings** | Bitrate | `25Mbit/s` |
| **Post-processing** | Sharpening | `Quality` |

> [!TIP]
> By setting **Anti-Aliasing to Disabled** in VRChat and using **Sharpening** in WiVRN, you can maintain readable text while significantly reducing the load on your integrated GPU.

## 5. Why these settings?

* **Anti-Aliasing: Disabled**
    * Massive FPS boost by significantly reducing the fragment shader workload on the iGPU.
* **WiVRN 50% Res + Sharpening**
    * Drastically saves VRAM and GPU cycles by rendering fewer pixels, while using a low-cost sharpening filter to maintain text readability.
* **jemalloc (`MALLOC_CONF`)**
    * Stabilizes RAM usage on systems where memory is shared between the CPU and GPU, preventing crashes during heavy asset loading.
* **Hardware Decoding**
    * Offloads in-game video playback to the GPU's dedicated media engine, preventing the CPU from bottlenecking the entire system.

---

## Disclaimer

This is a **"Performance First"** configuration. To achieve a playable VR experience on "Potato" hardware, you should expect lower visual fidelity, including shimmering and pixelation. Use this as a baseline and adjust to your preference.
