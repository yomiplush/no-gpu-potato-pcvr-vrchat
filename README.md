# Optimized Potato PCVR for VRChat (Flatpak + WiVRn)
<img src="https://for-github-resource.pages.dev/header.webp" width="800" alt="header"><br>

This repository provides an aggressive, **ToS-friendly** optimization guide for running VRChat 
on extremely low-end systems, integrated GPUs (iGPU), or **"Potato PCs."** 
### 🛡️ Compliance & Safety First
This guide is built with strict adherence to the **VRChat Terms of Service (ToS)**. 
* **NO Client Modding:** We do not modify VRChat's binaries, `.dll` files, or internal assets.
* **NO EAC Bypass:** This setup works fully within the Easy Anti-Cheat (EAC) environment.
* **Pure Optimization:** All performance gains come from Linux system-level tuning (Proton, DXVK, and GameMode)
* and official VRChat launch arguments.

While **CachyOS** is the recommended distribution for its optimized kernel and performance-oriented repository, 
this guide is applicable to all major Linux environments.

# Component,Details
* OS,CachyOS x86_64 (Kernel: 6.18.7-2-cachy)
* Host,LENOVO 20UGS2Q500 (ThinkPad X13 Gen 1)
* CPU,AMD Ryzen 5 PRO 4650U with Radeon Graphics
* GPU,AMD ATI Radeon Vega 6 (Integrated)
* Memory,8GB (Approx. 6573MiB detected)
* Network,Wi-Fi 6 / Wired

### Performance & Proof
* **Result:** Successfully rendered VRChat in PCVR mode with iGPU stuff laptop.
* **FPS:** Approx. [avg 31-38] FPS
* **Video Proof:** [Watch the video here](https://www.dailymotion.com/video/x9yukmi)

# Required hardware
* Internet Connection
* PC (Laptop or Desktop but atleast not need high-prices GPU stuff)
* Quest 1/2/3/3s or Pico HMD (or supported WiVRN apps even sideload)
* Linux OS (Recommend CachyOS Single-boot, but first trying i personally recommend dual-boot stuff your favor)

## 1. Prerequisites & Dependencies

To ensure all optimizations (like `jemalloc` and `VA-API` hardware decoding) function correctly, 
you must install the following on your host system based on your distribution and hardware.

### Step 1: Install Steam and required utility extensions
```Bash
flatpak install flathub com.valvesoftware.Steam
flatpak install flathub org.freedesktop.Platform.VulkanLayer.gamescope
flatpak install flathub com.valvesoftware.Steam.CompatibilityTool.Proton-GE
flatpak install flathub org.freedesktop.Platform.VAAPI.Mesa
flatpak install flathub net.davidotek.pupgui2
```

## 2. Compatibility Tool (Proton GE)
VRChat requires Proton GE for functional in-game video players (AVPro) and essential performance fixes.

# Open Steam.
Navigate to VRChat: Right-click VRChat in your library and select Properties.
Compatibility Tab: Go to the Compatibility section.
Enable Override: Check the box: "Force the use of a specific Steam Play compatibility tool."
Select Version: Choose GE-Proton (Flatpak) from the dropdown list.

## 🚀 Steam Launch Options (VRChat Optimized)

These launch options are optimized for running VRChat in a Linux (Proton) environment. 
Copy the command below and paste it into the **Launch Options** in your Steam game properties.

### 📋 Copy & Paste

```bash
DXVK_CONFIG="dxgi.maxDeviceMemory=4096" gamemoderun WINE_FULLSCREEN_FSR=1 WINE_FULLSCREEN_FSR_MODE=performance PRESSURE_VESSEL_IMPORT_OPENXR_1_RUNTIMES=1 PRESSURE_VESSEL_FILESYSTEMS_RW=/var/lib/flatpak/app/io.github.wivrn.wivrn %command% -screen-width 320 -screen-height 240 --enable-avpro-in-proton --enable-hw-video-decoding
```
### ⚠️ Notes
* Ensure **Feral GameMode** is installed; otherwise, please remove `gamemoderun` from the command.
* To apply these settings, right-click **VRChat** in Steam > **Properties** > **General** > **Launch Options**, then restart the game.

### 🔍 Parameter Breakdown

| Category | Command | Description |
| :--- | :--- | :--- |
| **Memory** | `DXVK_CONFIG="dxgi.maxDeviceMemory=..."` | Limits VRAM reporting to prevent crashes on systems with shared memory (iGPU). |
| **Graphics** | `WINE_FULLSCREEN_FSR=1` | Enables AMD FidelityFX Super Resolution for better visuals at low resolutions. |
| **Graphics** | `WINE_FULLSCREEN_FSR_MODE=performance` | Sets FSR to prioritize frame rate over image quality. |
| **System** | `gamemoderun` | Applies Feral GameMode to optimize CPU governor and process priority. |
| **VR / XR** | `PRESSURE_VESSEL_IMPORT_OPENXR_1_RUNTIMES=1` | Seamlessly imports host OpenXR runtimes into the Steam container. |
| **VRChat** | `--enable-avpro-in-proton` | Native support for AVPro video playback within the Proton environment. |
| **VRChat** | `--enable-hw-video-decoding` | Offloads video decoding to hardware to reduce CPU overhead. |
| **Window** | `-screen-width 320 -screen-height 240` etc. | Minimizes the desktop mirror resolution to save precious GPU/CPU cycles. |
| **Privacy** | `-nolog -no-analytics` | Disables local logging and telemetry to reduce disk I/O and overhead. |

## 4. Crucial In-Game & App Settings

For Potato PCVR, software-side optimization is just as critical as the launch commands.

### VRChat Settings (Graphics)
Adjust these within the VRChat Main Menu under **Settings** > **Graphics**:

<img src="https://for-github-resource.pages.dev/vrchat.jpg" width="800" alt="header"><br>

- **Quality Preset**: `Custom`
- **Anti-Aliasing**: **`Disabled`** (Crucial: AA is extremely heavy for iGPU resources)
- **Shadows**: `Low`
- **Level of Detail (LOD)**: `Low`

### WiVRN (Quest App) Settings
Configure these settings within the **WiVRN menu** on your headset:

<img src="https://for-github-resource.pages.dev/wivrn1.jpg" width="800" alt="header"><br>
<img src="https://for-github-resource.pages.dev/wivrn2.jpg" width="800" alt="header"><br>

| Tab | Setting | Recommended Value |
| :--- | :--- | :--- |
| **Settings** | Refresh Rate | `60` |
| **Settings** | Render Resolution | `50% (~916x1007 per eye)` |
| **Settings** | Bitrate | `25Mbit/s` |
| **Post-processing** | Sharpening | `Quality` |

> [!TIP]
> By setting **Anti-Aliasing to Disabled** in VRChat and using **Sharpening** in WiVRN,
> you can maintain readable text while significantly reducing the load on your integrated GPU.

## 5. Why these settings?

* **Anti-Aliasing: Disabled**
    * Massive FPS boost by significantly reducing the fragment shader workload on the iGPU.
* **WiVRN 50% Res + Sharpening**
    * Drastically saves VRAM and GPU cycles by rendering fewer pixels, while using a low-cost sharpening filter to maintain text readability.
* **Hardware Decoding**
    * Offloads in-game video playback to the GPU's dedicated media engine, preventing the CPU from bottlenecking the entire system.

---

⚠️ Disclaimer / Warning

This is a **"Performance First"** configuration. To achieve a playable VR experience on "Potato" hardware, 
you should expect lower visual fidelity, including shimmering and pixelation. Use this as a baseline and adjust to your preference.
Extremely Low Graphics Quality: To make VRChat run on non-GPU PCs, the resolution and visual quality are significantly reduced. Do not expect a standard VR experience.
Hardware Load: This method puts a heavy load on your CPU and integrated graphics (iGPU). Please use this at your own risk, as it may cause your PC to overheat.
