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

## Hardware Requirements

- ASTRO perimeter device with RK3288 chipset
- Android OS (minimum version per your application)
- LED hardware properly configured on device

## Documentation

Complete Java API documentation is available in the `Java_Docs/` directory. Open `Java_Docs/index.html` in a browser for full javadoc reference.

## Setup Instructions

See `LibrarySetup.docx` for detailed installation and integration steps.

## Version

- Released: May 17, 2021
- Library Version: 1.0

## Notes

- This is a compiled `.aar` library; source code is not included
- The library handles low-level communication with the RK3288 LED driver
- All color and effect changes are applied immediately
- State queries (getLEDColor, getLEDState) return current hardware state

## License

[Specify license if applicable]

## Support

For integration questions or issues, refer to the Cordova plugin examples in:
- `kylegmuir/Cordova-LED-Plugin`
- `kylegmuir/Cordova-LED-Example`

These repositories demonstrate real-world usage patterns.
