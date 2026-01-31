# 🏠 ESPHome Home Assistant Display

A beautiful, touch-enabled smart home control panel built with ESPHome and LVGL for the JC3248W535C ESP32-S3 display module. Control your lights, climate, and monitor energy usage directly from a sleek wall-mounted touchscreen.

<p align="center">
  <img src="images/ui-demo.jpg" alt="UI Demo" width="400">
</p>

---

## 📟 Device Specifications

| Specification | Details |
|---------------|---------|
| **Model** | JC3248W535C_I |
| **MCU** | ESP32-S3 (Dual-core, 240MHz) |
| **Display** | 3.5" IPS Capacitive Touch LCD |
| **Resolution** | 320 × 480 pixels |
| **PSRAM** | 8MB (Octal mode) |
| **Flash** | 16MB |
| **Touch Controller** | AXS15231 |
| **Display Interface** | QSPI |
| **Framework** | ESP-IDF |

### 🛒 Where to Buy

- **Display Module**: [AliExpress — Guition JC3248W535C](https://a.aliexpress.com/_EIyWvhw)

### 🖨️ 3D Printed Case

- **Desktop Enclosure**: [MakerWorld — Guition JC3248W535 3.5" ESP32 Desktop Enclosure](https://makerworld.com/en/models/1026987-guition-jc3248w535-3-5-esp32-desktop-enclosure?from=search#profileId-1009332)

---

## ✨ Features

### 🏡 Room-Based Control Pages
- **Bedroom** — Light controls, temperature & humidity monitoring
- **Living Room** — Multi-zone lighting, climate sensors
- **Bathroom** — Light and environmental controls
- **Corridor** — Hallway lighting
- **Outdoor** — External lighting and sensors

### 🌡️ Climate Control
- **Thermostat** — Underfloor heating control
- **Air Conditioning** — Per-room AC control with fan speed
- Temperature adjustment with visual feedback
- Real-time current & target temperature display

### ⚡ Energy Monitoring
- Grid power consumption
- Voltage monitoring
- Battery state of charge (SoC) with visual indicator
- Battery voltage & power flow
- Estimated runtime/charge time

### 💡 Smart Lighting
- Toggle switches for standard lights
- RGB light control with color picker
- Brightness adjustment via long-press
- Visual state feedback with icons

### ⚙️ Settings Panel
- Display brightness slider
- Auto screen-off toggle (60s timeout)
- Touch-to-wake functionality
- WiFi connection status

### 🎨 User Interface
- Modern dark theme with accent colors
- Smooth LVGL animations
- Header with WiFi status & page title
- Footer navigation between pages
- Boot screen with connection status
- Gesture-based navigation

---

## 📋 Prerequisites

- **Python 3.11** (Python 3.14 not yet supported by ESPHome)
- **Home Assistant** instance running on your network
- **ESPHome** integration enabled in Home Assistant
- **USB-C cable** for initial flashing

---

## 🚀 Setup

### Windows

```powershell
# Create virtual environment with Python 3.11
py -3.11 -m venv venv

# Activate virtual environment
.\venv\Scripts\Activate.ps1

# Install ESPHome
pip install esphome
```

### macOS / Linux

```bash
# Create virtual environment
python3.11 -m venv venv

# Activate virtual environment
source venv/bin/activate

# Install ESPHome
pip install esphome
```

---

## ⚙️ Configuration

### 1. Create `secrets.yaml`

Copy and customize your secrets file:

```yaml
# WiFi Configuration
wifi_ssid: "your-wifi-ssid"
wifi_password: "your-wifi-password"

# Home Assistant
ha_ip: "http://homeassistant.local:8123"
api_encryption_key: "your-api-encryption-key"

```

### 2. Generate API Encryption Key

Generate a secure key using:

```bash
python -c "import secrets, base64; print(base64.b64encode(secrets.token_bytes(32)).decode())"
```

### 3. Customize Entity IDs

Edit the page files in `layouts/pages/` to match your Home Assistant entity IDs:

```yaml
# Example: layouts/pages/bedroom.yaml
entity_id: switch.your_bedroom_light
```

---

## 🔧 Compile & Upload

### Via USB (First Time)

```powershell
# Activate venv first
.\venv\Scripts\Activate.ps1

# Compile and upload via USB
esphome run index.yaml
```

### Via Web Dashboard

```powershell
# Start the ESPHome dashboard
esphome dashboard .
```

Then open `http://localhost:6052` in your browser.

### Over-the-Air (OTA)

After initial USB flash, updates can be pushed wirelessly:

```powershell
esphome run index.yaml --device esphome-web-e90dec.local
```

---

## 📁 Project Structure

```
HomeAssistant_Display/
├── index.yaml              # Main entry point
├── device.yaml             # Hardware configuration (ESP32-S3, display, touch)
├── common.yaml             # WiFi, API, sensors, OTA config
├── secrets.yaml            # Credentials (not committed to git)
│
└── layouts/
    ├── main.yaml           # LVGL layout orchestrator
    ├── globals.yaml        # Global variables
    ├── scripts.yaml        # ESPHome scripts
    │
    ├── fonts/              # Custom fonts (Roboto, MDI icons)
    ├── themes/             # LVGL theme configuration
    ├── styles/             # Reusable widget styles
    │
    ├── pages/              # Room-specific pages
    │   ├── bedroom.yaml
    │   ├── living_room.yaml
    │   ├── bathroom.yaml
    │   ├── corridor.yaml
    │   ├── outdoor.yaml
    │   ├── energy.yaml
    │   └── climate.yaml
    │
    ├── sensors/            # Home Assistant sensor bindings
    │
    └── widgets/            # Reusable UI components
        ├── header/         # Top navigation bar
        ├── footer/         # Bottom navigation
        ├── boot_screen/    # Startup splash
        ├── settings_panel/ # Settings overlay
        ├── buttons/        # Button variants
        │   ├── icon_buttons/
        │   ├── text_buttons/
        │   └── icon_text_buttons/
        │       ├── light_buttons/
        │       ├── rgb_light_buttons/
        │       └── fan_buttons/
        ├── light_control_panel/   # Brightness slider overlay
        └── climate_control_panel/ # Temperature control overlay
```

---

## 🎯 Adding New Devices

### Adding a Light Switch

1. Edit the appropriate page in `layouts/pages/`
2. Add a button widget:

```yaml
- button:
    <<: !include
      file: ../widgets/buttons/icon_text_buttons/light_buttons/widget.yaml
      vars:
        uid: my_new_light
        entity_id: switch.my_light_entity
        text: "My Light"
        width: 49%
        height: 86
    on_short_click:
    - homeassistant.service:
        service: switch.toggle
        data: { entity_id: switch.my_light_entity }
```

3. Add the sensor binding in `layouts/sensors/` matching your page.

### Adding an RGB Light

```yaml
- button:
    <<: !include
      file: ../widgets/buttons/icon_text_buttons/rgb_light_buttons/widget.yaml
      vars:
        uid: my_rgb_light
        entity_id: light.my_rgb_entity
        text: "RGB Light"
        width: 49%
        height: 86
```

---

## 🔌 Pin Configuration

| Function | GPIO |
|----------|------|
| Display CS | 45 |
| Display CLK | 47 |
| Display Data | 21, 48, 40, 39 |
| Backlight PWM | 1 |
| I2C SDA (Touch) | 4 |
| I2C SCL (Touch) | 8 |

---

## 🐛 Troubleshooting

### Display Not Working
- Verify PSRAM is in octal mode at 80MHz
- Check SPI pins match your hardware revision

### Touch Not Responding
- Confirm I2C address `0x3B` for AXS15231
- Check SDA/SCL wiring

### WiFi Connection Issues
- Verify `secrets.yaml` credentials
- Check 2.4GHz network availability (5GHz not supported)

### Home Assistant Not Connecting
- Ensure API encryption key matches HA integration
- Verify HA is on the same network segment

---

## 📄 License

This project is open source. Feel free to use, modify, and distribute.

---

## 🙏 Acknowledgments

- [ESPHome](https://esphome.io/) — The foundation for this project
- [LVGL](https://lvgl.io/) — Powerful graphics library
- [Material Design Icons](https://materialdesignicons.com/) — Beautiful icon set
- [Home Assistant](https://www.home-assistant.io/) — Smart home platform
