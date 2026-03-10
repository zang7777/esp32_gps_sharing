```
╔══════════════════════════════════════════════════════════════════════════╗
║                                                                          ║
║   █████╗ ██████╗ ████████╗███████╗███╗   ███╗██╗███████╗                 ║
║  ██╔══██╗██╔══██╗╚══██╔══╝██╔════╝████╗ ████║██║██╔════╝                 ║
║  ███████║██████╔╝   ██║   █████╗  ██╔████╔██║██║███████╗                 ║
║  ██╔══██║██╔══██╗   ██║   ██╔══╝  ██║╚██╔╝██║██║╚════██║                 ║
║  ██║  ██║██║  ██║   ██║   ███████╗██║ ╚═╝ ██║██║███████║                 ║
║  ╚═╝  ╚═╝╚═╝  ╚═╝   ╚═╝   ╚══════╝╚═╝     ╚═╝╚═╝╚══════╝                 ║
║                                                                          ║
║        ⬡  Real-time GPS tracking. No SIM. No subscription.  ⬡           ║
╚══════════════════════════════════════════════════════════════════════════╝
```

<div align="center">

[![Platform](https://img.shields.io/badge/Hardware-ESP32-E74C3C?style=for-the-badge&logo=espressif&logoColor=white)](https://espressif.com)
[![Backend](https://img.shields.io/badge/Backend-Flask-000000?style=for-the-badge&logo=flask&logoColor=white)](https://flask.palletsprojects.com)
[![Firmware](https://img.shields.io/badge/Firmware-PlatformIO-F5822A?style=for-the-badge&logo=platformio&logoColor=white)](https://platformio.org)
[![License](https://img.shields.io/badge/License-MIT-10B981?style=for-the-badge)](LICENSE)
[![Language](https://img.shields.io/badge/C%2B%2B%20%7C%20Python%20%7C%20HTML-39.4%25%20%7C%2032.8%25%20%7C%2023%25-3B82F6?style=for-the-badge)]()

**Track any phone in real-time — using only an ESP32 and WiFi.**  
*No SIM card. No paid APIs. No monthly cost.*

</div>

---

## ⬡ The Problem

Every real-time GPS tracker solution out there has a catch:

```
┌─────────────────────────────────────────────────────────────────┐
│  Commercial trackers    →  $10–30/month SIM data plan           │
│  Google Maps sharing    →  Requires internet on tracked device  │
│  GSM modules (SIM800)   →  Extra hardware + carrier required    │
│  BLE trackers           →  10 metre range only                  │
│                                                                 │
│  Artemis               →  ESP32 + WiFi. That's it. Free.       │
└─────────────────────────────────────────────────────────────────┘
```

---

## ⬡ How It Works

The magic is the ESP32 running in **dual WiFi mode simultaneously** — Access Point + Station at the same time.

```
                    ┌─────────────────────────────────────┐
                    │                                     │
   📱  Phone        │        ESP32 Bridge                 │      💻 Server
  ─────────         │  ┌──────────────┐ ┌─────────────┐  │   ─────────────
  Connects to  ───▶ │  │  AP Mode     │ │Station Mode │  │ ▶  Flask App
  ESP32 hotspot     │  │ 192.168.4.1  │ │ Home WiFi   │  │    Live Dashboard
  Opens browser     │  └──────────────┘ └─────────────┘  │    Real-time Map
  Shares GPS data   │         ↕ bridges data ↕            │
                    └─────────────────────────────────────┘

  Phone needs NO internet.     Server gets live GPS feed.
```

The phone connects to the **ESP32's own hotspot** (no internet required on phone), opens a browser page, and hits "Start Sharing". The ESP32 receives the GPS/IMU data and forwards it over the home network to the Flask server — where an admin can watch everyone's location update live on a map.

---

## ⬡ Features

```
 Hardware
 ─────────────────────────────────────────────────────
  ⬡  ESP32 dual WiFi mode    AP + Station simultaneously
  ⬡  Zero extra hardware     no GPS module, no SIM800
  ⬡  Phone as sensor         uses browser Geolocation API
  ⬡  IMU data support        accelerometer / gyroscope

 Software
 ─────────────────────────────────────────────────────
  ⬡  Captive portal          auto-opens on phone connect
  ⬡  Real-time forwarding    sub-second GPS relay
  ⬡  Flask admin dashboard   live map, multiple devices
  ⬡  Multi-device support    track several phones at once
  ⬡  Cross-platform          Windows + Linux server support
  ⬡  Zero subscription       runs entirely on local network
```

---

## ⬡ System Architecture

| Component | Role | IP Address |
|---|---|---|
| 📱 **Phone** | Sends GPS + IMU data via browser | `192.168.4.x` |
| ⬡ **ESP32** | Dual-WiFi bridge (AP + Station) | `192.168.4.1` (AP side) |
| 💻 **Laptop/Server** | Runs Flask, shows live map | Your local IP (e.g. `192.168.1.x`) |

---

## ⬡ Quick Start

### 1 — Flash the ESP32

Open `src/config.h` and set your credentials:

```cpp
// Phone connects to this hotspot
const char *WIFI_SSID     = "Artemis";
const char *WIFI_PASSWORD = "12345678";

// ESP32 connects to your home WiFi
const char *HOME_WIFI_SSID     = "your_home_wifi";
const char *HOME_WIFI_PASSWORD = "your_password";

// Your laptop's IP on the home network
const char *FLASK_SERVER_IP = "192.168.x.x";  // run ipconfig / ip a
```

Upload via PlatformIO:
```
PlatformIO → Upload
```

### 2 — Start the Server

```bash
# Linux / Mac
bash start_server.sh

# Windows
.\start_server.bat

# Manual
cd flask_server
pip install flask
python app.py
```

Dashboard opens at: **http://localhost:5000**

### 3 — Track a Phone

1. Connect phone to WiFi: **`Artemis`** (password: `12345678`)
2. Open browser → **`http://192.168.4.1`**
3. Enter a name → tap **Start Sharing**
4. Watch it appear on the admin map instantly

---

## ⬡ Project Structure

```
artemis/
│
├── src/
│   ├── main.cpp          ← ESP32 firmware — dual WiFi bridge logic
│   └── config.h          ← All credentials and server settings
│
├── flask_server/
│   ├── app.py            ← Flask backend — receives and serves GPS data
│   └── templates/        ← Admin dashboard HTML + live map UI
│
├── platformio.ini         ← Board config (ESP32 DevKit)
├── start_server.sh        ← One-click server launch (Linux/Mac)
├── start_server.bat       ← One-click server launch (Windows)
└── convert_html.py        ← Utility: embeds assets into ESP32 firmware
```

---

## ⬡ Troubleshooting

**ESP32 won't connect to home WiFi**
- Use 2.4GHz only — ESP32 does not support 5GHz
- Double-check `HOME_WIFI_SSID` spelling in `config.h`
- Move ESP32 closer to the router during setup

**Phone browser won't load the page**
- Make sure phone is connected to `Artemis` hotspot, not home WiFi
- Turn off mobile data (4G/5G) — it overrides the local connection
- Try `http://192.168.4.1` manually if captive portal doesn't open

**Dashboard not receiving data**
- Run `ipconfig` (Windows) or `ip a` (Linux) and update `FLASK_SERVER_IP`
- Allow Python through Windows Defender Firewall on port 5000
- Confirm laptop and ESP32 are on the same home network

---

## ⬡ Use Cases

```
✦  Family location sharing      no subscription, works at home
✦  Workshop / warehouse         track workers on local network  
✦  Event coordination           festival staff, campus ops
✦  Robotics / drone tracking    mount phone, stream to ROS
✦  Field research               GPS logging without cell coverage
```

---

## ⬡ What Makes This Different

Most GPS sharing solutions need either:
- A SIM card in the tracked device, or
- Both devices on the internet simultaneously

Artemis needs **neither**. The phone only needs to be in WiFi range of the ESP32. The ESP32 handles all the bridging. This opens up use cases in basements, warehouses, underground, or any place with local WiFi but no cell signal.

---

## ⬡ Built With

- **ESP32** — dual WiFi mode (AP + Station)
- **PlatformIO** — firmware build system
- **Flask** — Python backend
- **Leaflet.js** — interactive map in dashboard
- **Browser Geolocation API** — GPS source on phone

---

## ⬡ License

MIT — build freely, hack freely.

---

<div align="center">

```
no SIM · no subscription · no limits
```

*built with an ESP32 and stubbornness*

</div>
