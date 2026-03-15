# 💧 Water Pump Automation System

> IoT automation system controlling a water pump that supplies water to an entire neighbourhood. Running continuously for over a year.

---

## Overview

A safety-critical embedded system built on an ESP8266 microcontroller that automates a water pump serving residential water supply. The system runs on a defined schedule and supports remote on-demand control via a Telegram bot, with a built-in safety time limit to prevent over-run.

**Status:** 🟢 Production — 1+ year uptime  
**Hardware:** ESP8266 + relay module  
**Control interface:** Telegram Bot API  
**Criticality:** High — failure affects neighbourhood water supply

---

## Why This Matters

This is not a hobby LED blinker. A failure in this system means:
- No water for an entire neighbourhood
- Potential pump damage from dry-running
- Flooding risk from uncontrolled operation

Every design decision was made with this in mind.

---

## Architecture

```
                    ┌─────────────────┐
                    │  Telegram Bot   │
                    │  (remote ctrl)  │
                    └────────┬────────┘
                             │ HTTPS API
                             ▼
                    ┌─────────────────┐
                    │    ESP8266      │
                    │  ┌───────────┐  │
                    │  │ Scheduler │  │
                    │  │ (cron-like│  │
                    │  │  logic)   │  │
                    │  └─────┬─────┘  │
                    │        │        │
                    │  ┌─────▼─────┐  │
                    │  │  Safety   │  │
                    │  │  Timer    │  │
                    │  │ (1hr max) │  │
                    │  └─────┬─────┘  │
                    └────────┼────────┘
                             │ GPIO
                             ▼
                    ┌─────────────────┐
                    │  Relay Module   │
                    └────────┬────────┘
                             │ 240V switched
                             ▼
                    ┌─────────────────┐
                    │   Water Pump    │
                    └─────────────────┘
```

---

## Features

### Scheduled Operation
- Pump runs automatically on a defined daily schedule
- Schedule stored in firmware, survives power cycles
- Timing logic handles edge cases (midnight crossover, DST)

### Remote Control via Telegram
- Send `/on 60` to run pump for 60 minutes
- Send `/off` to stop immediately
- Send `/status` to check current state and next scheduled run
- Bot only responds to authorised user IDs — no public access

### Safety Systems
- **Hard time limit:** Pump cannot run for more than 1 hour per remote command, regardless of input
- **Watchdog timer:** ESP8266 hardware watchdog restarts the chip if firmware hangs
- **Relay fail-safe:** Relay defaults to OFF on power loss — pump does not run without active signal

---

## Hardware

| Component | Purpose |
|---|---|
| ESP8266 (NodeMCU) | Main controller, WiFi, logic |
| 5V Relay Module | Switches 240V pump circuit |
| 5V Power Supply | Powers ESP8266 + relay |
| Enclosure | Weatherproofing |

---

## Key Skills Demonstrated

- Embedded C / MicroPython firmware development
- Hardware interfacing (GPIO, relay control, 240V switching)
- REST API integration (Telegram Bot API over HTTPS)
- Safety-critical system design (timeouts, watchdogs, fail-safes)
- Long-term reliability engineering (1+ year unattended operation)
- IoT security (authorised user allowlist, no open ports)

---

## Lessons Learned

- The ESP8266 WiFi stack can wedge under certain conditions — the hardware watchdog is not optional for unattended systems
- Relay coil inrush can cause ESP8266 brownouts; a decoupling capacitor on the power rail fixed intermittent resets
- Always design for power loss recovery — the system must return to a safe, known state after any outage

---

*Part of [spy1345a's portfolio](../README.md)*
