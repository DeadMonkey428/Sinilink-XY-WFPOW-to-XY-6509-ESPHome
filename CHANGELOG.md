# Changelog

Alle nennenswerten Änderungen an diesem Projekt werden hier dokumentiert.

Das Format orientiert sich an [Keep a Changelog](https://keepachangelog.com/de/1.1.0/),
und das Projekt folgt [Semantic Versioning](https://semver.org/lang/de/).

> Die Versionen 0.1.0–0.4.0 sind aus der Entwicklungshistorie rekonstruiert und
> wurden nicht einzeln getaggt; erst **0.5.0** ist ein echtes Release.

## [Unreleased]

## [0.5.1] – 2026-07-19

### Changed
- Diagnose-Sensoren **Modellnummer** und **Firmwareversion** pollen wieder im
  1-s-Takt (vorher `skip_updates: 59` ≈ 60 s), damit sie nach dem Boot ohne
  Minutenverzögerung erscheinen.

### Removed
- **WiFi-Status-Sync** (Schreiben des Verbindungszustands in Register `0x001E`,
  inkl. `on_boot`-/`wifi`-Trigger, Sync-Script und 15-s-Heartbeat). Das
  Schreiben in dieses undokumentierte Register löste am XY-6509 unerwünschtes
  Verhalten aus (Ausgang schaltete nach dem Einschalten wieder ab).

## [0.5.0] – 2026-07-19

Erstes getaggtes Release. API-Verschlüsselung ist ab hier verpflichtend.

### Added
- Geräte-Test-Checkliste in der README („Zu testen (Verifikation am Gerät)").
- Dieses CHANGELOG.

### Changed
- Native-API-Verschlüsselung ist nun **Pflicht** (nur noch eine, verschlüsselte
  Konfiguration).

### Removed
- Variante ohne API-Verschlüsselung (`esphome-no-encryption.yaml`) und alle
  Verweise darauf.

## [0.4.0] – 2026-07-19

Korrekturen aus dem Pre-Release-Review und Robustheit des WiFi-Status.

### Added
- Auswahl **„Temperatureinheit"** (Celsius/Fahrenheit, Register `0x0013`).
- `skip_updates` auf dem WiFi-Statusregister `0x001E` zur Bus-Entlastung.

### Changed
- **Displayhelligkeit** erlaubt jetzt `0`–`5` (vorher `1`–`5`; laut Handbuch
  ist `0` die dunkelste Stufe).
- **Sollstrom**-Maximum auf `9.000 A` begrenzt (OCP-Grenzwert bleibt `9.200 A`).
- **WiFi-Status** wird zusätzlich per Heartbeat (alle 15 s) aufgefrischt, falls
  die XY-6509-Firmware ein periodisches Update erwartet.
- `api: reboot_timeout: 0s` – kein Auto-Neustart bei fehlender HA-Verbindung.

## [0.3.0] – 2026-07-18

Presets speichern, restliche Gerätefunktionen und CI.

### Added
- **Preset speichern:** Button „Aktuelle Werte als Preset speichern" +
  Zielauswahl M0–M9; schreibt die aktuellen Werte per Write-Multiple (`0x10`)
  in den Block `0x0050 + Mx·0x10`.
- Neue Entities: **Summer**, **MPPT** (Ein/Aus + Koeffizient),
  **Konstantleistung** (Ein/Aus + Wert), **Ladeschluss-Strom**,
  **Display-Ausschaltzeit**, **Temperatur-Kalibrier-Offsets** (intern/extern).
- Modbus-Referenz als PDF (`doc/Sinilink XY-6509.pdf`) mit Verweisen in der
  README.
- GitHub-Actions-CI: validiert **und kompiliert** die Konfiguration bei jedem
  Push (Struktur + C++-Lambda).

### Changed
- **Tastensperre** von read-only (`binary_sensor`) auf schreibbaren `switch`
  umgestellt.

## [0.2.0] – 2026-07-18

Sicherheit, Recovery und Dokumentation.

### Added
- WPA2-**Fallback-AP** + Captive Portal als Rettungszugang.
- README mit Installationsanleitung (web.esphome.io), Pinout-Bild und
  Verdrahtungstabelle.
- Zwei Konfigurationsvarianten (mit/ohne API-Verschlüsselung).
  _(Die Variante ohne Verschlüsselung wurde in 0.5.0 wieder entfernt.)_

### Security
- Native-**API-Verschlüsselung**, **OTA-Passwort**, `min_auth_mode: WPA2`.

## [0.1.0] – 2026-07-18

Grundkonfiguration (Ausgangsstand des Projekts).

### Added
- Modbus-RTU-Anbindung des XY-6509 (Adresse `0x01`, 115200 8N1) über die
  ESP8285-Hardware-UART.
- **Messwerte:** Ausgangsspannung/-strom/-leistung, Eingangsspannung,
  Kapazität, Energie, Laufzeit, interne/externe Temperatur, Modell/Firmware.
- **Steuerung:** Sollspannung, Strombegrenzung, Ausgang ein/aus,
  Auto-Einschalten nach Gerätestart, Displayhelligkeit.
- **Schutzgrenzen** OVP/LVP/OCP/OPP/OAH/OWH/OHP/OTP/ETP mit Ein/Aus-Schaltern
  (letzter Grenzwert wird gemerkt).
- **Preset laden** (M0–M9), Schutzstatus als Klartext, Button „Alarm
  quittieren", Konstantstrom- und Tastensperre-Anzeige.
- **WiFi-Status-Sync** aufs XY-6509-Display und Status-LED.

[Unreleased]: https://github.com/DeadMonkey428/Sinilink-XY-WFPOW-to-XY-6509-ESPHome/compare/v0.5.1...HEAD
[0.5.1]: https://github.com/DeadMonkey428/Sinilink-XY-WFPOW-to-XY-6509-ESPHome/compare/v0.5.0...v0.5.1
[0.5.0]: https://github.com/DeadMonkey428/Sinilink-XY-WFPOW-to-XY-6509-ESPHome/releases/tag/v0.5.0
