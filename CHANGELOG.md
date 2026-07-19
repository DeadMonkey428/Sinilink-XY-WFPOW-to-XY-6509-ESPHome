# Changelog

Alle nennenswerten Änderungen an diesem Projekt werden hier dokumentiert.

Das Format orientiert sich an [Keep a Changelog](https://keepachangelog.com/de/1.1.0/),
und das Projekt folgt [Semantic Versioning](https://semver.org/lang/de/).

## [Unreleased]

## [0.5.0] – 2026-07-19

Erste Veröffentlichung. Bindet das **Sinilink XY-WFPOW** (ESP8285) per
**Modbus RTU** an das **XY-6509** an und stellt es vollständig in Home
Assistant bereit.

### Added
- **Messwerte:** Ausgangsspannung/-strom/-leistung, Eingangsspannung,
  Kapazität, Energie, Laufzeit, interne/externe Temperatur, Modell/Firmware.
- **Steuerung:** Sollspannung, Strombegrenzung, Ausgang ein/aus,
  Auto-Einschalten nach Gerätestart, Displayhelligkeit (0–5),
  Display-Ausschaltzeit, Summer, Tastensperre, Temperatureinheit,
  MPPT- und Konstantleistungs-Modus.
- **Presets M0–M9 laden und speichern** (Block-Write an `0x0050 + Mx·0x10`).
- **Schutzfunktionen:** OVP/LVP/OCP/OPP/OAH/OWH/OHP/OTP/ETP mit Ein/Aus-
  Schaltern (letzter Grenzwert wird gemerkt), Schutzstatus als Klartext,
  Button „Alarm quittieren".
- **Kalibrierung:** Temperatur-Offsets intern/extern.
- **WiFi-Status-Sync** aufs XY-6509-Display (event-getrieben + Heartbeat).
- **Rohwert-Register** (unbestätigte Skalierung): MPPT-Koeffizient (`0x0020`),
  Ladeschluss-Strom (`0x0021`), Konstantleistung-Wert (`0x0023`).
- **Dokumentation:** README mit Installationsanleitung (web.esphome.io),
  Pinout-Bild, Modbus-Referenz (PDF) und Geräte-Test-Checkliste.
- **CI:** GitHub Actions validiert und kompiliert die Konfiguration bei jedem
  Push (Struktur + C++-Lambda).

### Security
- **API-Verschlüsselung ist Pflicht** (native ESPHome-API mit `encryption`).
- OTA-Passwort und WPA2-Fallback-AP + Captive Portal.
- `reboot_timeout: 0s` – kein Auto-Neustart bei fehlender HA-Verbindung.

[Unreleased]: https://github.com/DeadMonkey428/Sinilink-XY-WFPOW-to-XY-6509-ESPHome/compare/v0.5.0...HEAD
[0.5.0]: https://github.com/DeadMonkey428/Sinilink-XY-WFPOW-to-XY-6509-ESPHome/releases/tag/v0.5.0
