# ASTRO_LED Library

Android library for controlling RGB LED displays on ASTRO perimeter devices using RK3288 chipset.

## Overview

The `ledControl.aar` library provides a Java interface to control LED status, color, and lighting effects on ASTRO display hardware. This library is used in PowerBx AV display solutions to manage LED indicators, status lights, and decorative RGB elements.

## What It Does

- Control LED power state (on/off)
- Set LED colors (16 RGB color options)
- Apply lighting effects (strobe, flash, fade, smooth)
- Query current LED state and color

## Components

### Core Class: LedController

Single public class with static enums for colors and commands.

**Methods:**
- `on()` — Turn LEDs on
- `off()` — Turn LEDs off
- `setLEDColor(COLORS color)` — Set active color
- `setStrobe()` — Strobe effect
- `setFlash()` — Flash effect
- `setFade()` — Fade effect
- `setSmooth()` — Smooth effect
- `getLEDColor()` — Get current color (returns COLORS enum)
- `getLEDState()` — Get current state (returns COMMANDS enum: ON/OFF/STROBE/etc)

### Available Colors (16 total)

```
RED (0x04)              GREEN (0x05)           BLUE (0x06)
WHITE (0x07)            RED_ORANGE (0x08)      MINT (0x09)
PURPLE (0x0a)           ORANGE (0x0c)          TURQUOISE (0x0d)
PURPLE_PINK (0x0e)      ORANGE_YELLOW (0x10)   LIGHT_BLUE (0x11)
PINK (0x12)             YELLOW (0x14)          TEAL (0x15)
MAGENTA (0x16)
```

### Available Commands

```
ON (0x03)
OFF (0x02)
FLASH (0x0b)
STROBE (0x0f)
FADE (0x13)
SMOOTH (0x17)
```

## Installation

### 1. Add to Project

Copy `ledControl.aar` to your Android project's `libs/` directory:

```
app/libs/ledControl.aar
```

### 2. Update build.gradle (Module: app)

```gradle
dependencies {
    implementation fileTree(dir: "libs", include: ["*.aar"])
    // ... other dependencies
}

android {
    // ... other config
    repositories {
        flatDir {
            dirs 'libs'
        }
    }
}
```

### 3. Import in Java

```java
import com.example.ledcontrol.LedController;
import com.example.ledcontrol.LedController.COLORS;
import com.example.ledcontrol.LedController.COMMANDS;
```

## Usage Examples

### Basic Operations

```java
// Initialize controller
LedController ledController = new LedController();

// Turn LEDs on
ledController.on();

// Set color to red
ledController.setLEDColor(COLORS.RED);

// Apply strobe effect
ledController.setStrobe();

// Turn LEDs off
ledController.off();
```

### Check Current State

```java
// Get current LED state
LedController.COMMANDS currentState = ledController.getLEDState();
if (currentState == LedController.COMMANDS.ON) {
    Log.d("LED", "LEDs are on");
}

// Get current color
LedController.COLORS currentColor = ledController.getLEDColor();
Log.d("LED", "Current color: " + currentColor);
```

### Lighting Effects

```java
// Flash effect (rapid on/off)
ledController.setFlash();

// Strobe effect (synchronized flashing)
ledController.setStrobe();

// Fade effect (smooth transition)
ledController.setFade();

// Smooth effect (continuous smoothing)
ledController.setSmooth();
```

## How It Works (Technical Details)

The `ledControl.aar` library communicates with the RK3288 through a kernel sysfs interface:

```
Application Code (Java)
    ↓
LedController.aar (thin wrapper)
    ↓
/sys/devices/platform/led_con_h/zigbee_reset (sysfs device file)
    ↓
RK3288 Kernel LED Driver
    ↓
GPIO/PWM Hardware Control
```

**Key points:**
- The library relies on a custom RK3288 kernel module
- No external hardware controller or vendor dependency
- Commands are sent as hex values to a kernel device file
- Root access is automatically handled by the library
- All state changes are applied to the hardware immediately

## Hardware Requirements

- ASTRO perimeter device with RK3288 chipset
- Android OS (minimum version per your application)
- LED hardware properly configured on device
- Kernel module `led_con_h` compiled and loaded into the RK3288 kernel

## Documentation

Complete Java API documentation is available in the `Java_Docs/` directory. Open `Java_Docs/index.html` in a browser for full javadoc reference.

## Setup Instructions

See `LibrarySetup.docx` for detailed installation and integration steps.

## Version

- Released: May 17, 2021
- Library Version: 1.0

## Important Notes

- **Compiled library** — Source code is not included; only the `.aar` binary is provided
- **Kernel-dependent** — Requires the RK3288 LED kernel driver to be present and functional
- **Immediate execution** — All color and effect changes are applied immediately to hardware
- **State persistence** — getLEDColor() and getLEDState() return the current hardware state
- **Root requirement** — The library internally uses root (`su`) to write to kernel device files
- **Brightness controls** — The Cordova plugin exposes brightnessUp/brightnessDown (0x00, 0x01), but these are not exposed in the native `.aar` class; use the Cordova plugin if brightness control is needed
- **No effect parameters** — Effects (fade, smooth, strobe, flash) run with fixed timing; timing is not configurable via the library

## Troubleshooting

### LEDs Not Responding
- Verify the RK3288 kernel module is loaded: `lsmod | grep led` (on device shell)
- Check if `/sys/devices/platform/led_con_h/zigbee_reset` exists (on device shell)
- Ensure the app has root access via `su` command
- Verify LED hardware is properly connected to the RK3288 board

### Effects Not Working
- Some effects may have hardware-dependent timing
- Test with basic on/off commands first, then try effects
- Brightness-related effects may depend on PWM availability

### Compatibility Issues
- This library is specific to RK3288 and the ASTRO LED driver
- It will not work on other Android devices without the matching kernel driver
- For other Rockchip SoCs (RK3566, RK3588, etc.), a separate driver and potentially a recompiled `.aar` is required

## License

[Specify license if applicable]

## Support

### For Integration Help
Refer to the Cordova plugin repositories for real-world usage examples:
- [`kylegmuir/Cordova-LED-Plugin`](https://github.com/kylegmuir/Cordova-LED-Plugin) — Full plugin implementation
- [`kylegmuir/Cordova-LED-Example`](https://github.com/kylegmuir/Cordova-LED-Example) — Example app using the plugin

These demonstrate how to properly initialize, set colors, and apply effects.

### For Hardware/Driver Issues
Contact the ASTRO device vendor or Rockchip support:
- Confirm the LED kernel driver is included in your RK3288 board image
- Request the driver source if you need to modify or port it to another platform
- Verify GPIO/PWM pin assignments if implementing custom LED hardware

### For Third-Party Integration
If you're integrating this into an external app:
1. Verify you're running on an ASTRO device with the matching LED driver
2. Use the Cordova plugin wrapper if building a cross-platform app
3. Use the native `.aar` only if building an app that runs on the ASTRO device itself
4. See the migration guide (in PowerBx Drive) if considering hardware upgrades (e.g., RK3288 → RK3566)
