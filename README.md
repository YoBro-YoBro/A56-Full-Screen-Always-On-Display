# Samsung Galaxy A56 Flagship AOD Enabler

A systemless **Magisk / KernelSU / APatch** root module engineered specifically for the **Samsung Galaxy A56**. Enables Flagship level Fullscreen Aod 

---

## Overview & Background

On Samsung One UI devices, system-level capabilities and hardware feature sets are controlled by an XML configuration file located in `/system/etc/floating_feature.xml` or `/vendor/etc/floating_feature.xml`. Even though the Galaxy A56 is equipped with a vibrant Super AMOLED panel capable of running modern AOD effects, Samsung disables full-screen wallpaper dimming and fluid clock morphing in software to differentiate its A-series lineup from flagship models.

This module bridges that gap without modifying your actual `/system` or `/vendor` partitions. By operating systemlessly during the early `post-fs-data` boot stage, it dynamically injects and updates the exact floating feature flags required by Samsung's `SystemUI` and `AODService` packages—allowing you to enjoy the complete flagship Always On Display experience safely and reversibly.

---

## What Does This Module Do?

At every boot, the module's `post-fs-data.sh` script locates the active `floating_feature.xml` configuration and applies four targeted modifications:

| Floating Feature XML Tag | Value | Technical Function & User Benefit |
| :--- | :---: | :--- |
| `SEC_FLOATING_FEATURE_LCD_CONFIG_AOD_FULLSCREEN` | `1` | **Unlocks Full-Screen Wallpaper AOD:** Allows your lockscreen wallpaper to remain visible in a dimmed, low-power state on the Always On Display instead of fading to a pure black screen. Enables the *"Show Lock screen wallpaper"* toggle inside AOD settings. |
| `SEC_FLOATING_FEATURE_FRAMEWORK_CONFIG_AOD_ITEM` | `aodversion=7,clocktransition` | **Enables AOD v7 & Clock Transitions:** Upgrades the AOD framework profile to version 7 and activates smooth spatial morphing animations when transitioning between your Lockscreen clock and AOD clock. |
| `SEC_FLOATING_FEATURE_LCD_CONFIG_AOD_BRIGHTNESS_ANIMATION` | `0` | **Direct Brightness Switching:** Bypasses the gradual flagship LTPO brightness ramp-down curve so the display controller jumps directly to its configured AOD luminance level. |
| `SEC_FLOATING_FEATURE_LCD_SUPPORT_AMOLED_DISPLAY` | `TRUE` | **AMOLED Capability Enforcement:** Explicitly confirms AMOLED panel support to One UI framework services, preventing hidden fallbacks or disabled AOD sub-menus. |

---

## How It Works Under the Hood

* **Smart Coexistence with `a56_to_flagship`:**
  Many Galaxy A56 users already run the `a56_to_flagship` module, which mounts its own modified `floating_feature.xml`. If two modules try to mount the same XML file independently, one will overwrite the other. At boot, this script checks whether `/data/adb/modules/a56_to_flagship` is present and enabled. If found, it injects the AOD flags directly into `a56_to_flagship`'s XML file and cleans up its own local mirror directories to guarantee zero mount conflicts.
* **Standalone Fallback Mode:**
  If `a56_to_flagship` is not installed (or is disabled), the script automatically detects whether your firmware stores its stock configuration in `/system/etc/floating_feature.xml` or `/vendor/etc/floating_feature.xml`, copies it into the module's overlay directory, and patches it cleanly.
* **Non-Destructive XML Parsing:**
  Rather than replacing your entire `floating_feature.xml` with a static file from another firmware build (which can cause bootloops or break device-specific hardware features), the script edits your device's exact XML in place. If a tag already exists, its value is updated; if a tag is absent, it is cleanly appended right before the closing `</SecFloatingFeatureSet>` root tag with proper `0644` (`rw-r--r--`) permissions.

---

## Compatibility & Requirements

* **Supported Device:** Samsung Galaxy A56 (All regional variants, including `SM-A566B` and `SM-A566E`)
* **Supported Firmware:** One UI 7 to One UI 9.5 (Android 15+)
* **Root Environment:** Magisk (v26+), KernelSU, or APatch

---

## Installation Guide

1. Download the latest release `.zip` archive from the **Releases** page.
2. Open your root manager (**Magisk**, **KernelSU**, or **APatch**).
3. Navigate to the **Modules** tab and tap **Install from storage**.
4. Select the downloaded ZIP archive and wait for the installation to complete.
5. **Reboot** your device.
6. Go to **Settings > Lock screen and AOD > Always On Display** to customize your newly unlocked Full-Screen Wallpaper and transition settings.

---

## Known Notes & Uninstallation

* **Screen-Off Transition Delay:** Because the Galaxy A56 uses a standard Super AMOLED panel rather than a variable-refresh LTPO panel found on Galaxy S-series flagships, you may observe a brief **1–2 second pause** after locking the phone before AOD settles into its low-power state. This is an expected hardware/One UI behavior when running `AOD_FULLSCREEN=1` on non-LTPO displays.
* **How to Uninstall:** Open your root manager, toggle off or remove the module, and reboot. Your device will immediately revert to stock AOD behavior with zero leftover changes.

---

## Credits

* **YoBro-YoBro**
* **Fall**
* **Gemini**

---

## Disclaimer

This module modifies system floating feature overlays and requires an unlocked bootloader with root access. While all modifications are applied systemlessly, always ensure you know how to disable modules via Safe Mode or custom recovery before flashing. Use at your own risk.
