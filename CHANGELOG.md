# Changelog

Alle nennenswerten Änderungen an diesem Projekt werden hier dokumentiert.

Das Format orientiert sich an [Keep a Changelog](https://keepachangelog.com/de/1.1.0/),
und das Projekt folgt [Semantic Versioning](https://semver.org/lang/de/).

> Die Versionen 0.1.0–0.4.0 sind aus der Entwicklungshistorie rekonstruiert und
> wurden nicht einzeln getaggt; erst **0.5.0** ist ein echtes Release.

## [Unreleased]

## [0.5.6] – 2026-08-23

### Added
- **`dashboard_import`**: Das Repo ist jetzt Update-Quelle. Die lokale
  Konfiguration im ESPHome-Dashboard besteht nur noch aus einem
  `packages:`-Verweis auf `esphome.yaml@main`; neue Versionen meldet Home
  Assistant dann als Update statt Copy-Paste. README entsprechend erweitert.

## [0.5.5] – 2026-08-23

### Changed
- **ESPHome ≥ 2026.8.0 erforderlich** (`min_version`). Der Block-Read des
  Profil-Editors nutzt die neue Modbus-API (`modbus::EntityType`,
  `std::span`); mit ESPHome 2026.8 kompilierte die alte Signatur nicht mehr.
- **M0 wieder im Profil-Editor wählbar** (Dropdown „Profil anzeigen" M0–M9).
  Ohne M0 war nicht sichtbar, was im aktiven Arbeitsdatensatz steht.
  Slot-Adressierung in den Lambdas wieder `active_index()` (ohne +1).
  Der Hinweis bleibt: V/I-Edits bei M0 wirken live bzw. werden überschrieben.

## [0.5.4] – 2026-08-15

### Changed
- **Schrittweiten** der Sollwerte und Alarmgrenzen vereinheitlicht (Live-Entitäten
  und Profil-Editor): Sollspannung, Strombegrenzung, LVP, OVP, OCP, OAH, OWH
  auf `0.1`, OPP auf `1`. OHP, OTP, ETP bleiben `1`.

## [0.5.3] – 2026-08-09

### Changed
- **Profil-Editor bietet nur noch M1–M9 an.** M0 ist der aktive
  Arbeitsdatensatz, dessen Werte die Live-Entitäten bereits zeigen; Edits dort
  wirkten live bzw. wurden überschrieben. Slot-Adressierung in den Lambdas
  entsprechend korrigiert (Listenindex + 1).
- **Dokumentation M0:** Der M0-Block ist der aktive Arbeitsdatensatz (die
  Live-Alarmgrenzen `0x0052–0x005E` liegen im selben Block). Sollspannung/
  Strombegrenzung im Profil-Editor bei M0 zu ändern wirkt live bzw. wird von
  Live-Werten überschrieben – Geräteverhalten. Hinweis in README und YAML.

### Removed
- **Display-Ausschaltzeit** (`0x0015`) entfernt. Das dauerhafte Modbus-Polling
  (1 s) setzt den Screen-Off-Timer des XY-6509 vermutlich ständig zurück, sodass
  der LCD nie abschaltet – die Entität war damit wirkungslos und irreführend.

## [0.5.2] – 2026-08-04

### Fixed
- Modbus-`send_wait_time` von 5 ms auf 100 ms erhöht. Das XY-6509 antwortet
  erst nach ~34 ms; mit 5 ms lief ESPHome in Timeouts und verwarf verspätete
  Antworten (`unexpected frame`, `Stop waiting for response`) → träge/lückenhafte
  Messwert-Updates. Behebt u.a. das verzögerte Aktualisieren der Spannung.

### Added
- **Profil-Editor:** Dropdown „Profil anzeigen" (M0–M9) liest den Register-Block
  des gewählten Profils (`0x0050 + Mx·0x10`) und zeigt dessen Werte
  (Sollspannung, Strombegrenzung, alle Schutzgrenzen, „Ausgang nach
  Gerätestart") als **editierbare** „Profil: …"-`number`-Entitäten. Jede
  Änderung wird sofort in den gewählten Slot geschrieben – ohne das Profil zu
  laden. (M1/M2 = Tasten-Presets des Geräts.)
- `project:` (Name + Version) im `esphome:`-Block — die Firmware-Version
  erscheint jetzt in Home Assistant (Geräteinfo) und im Boot-Log. Muss bei
  jedem Release mit CHANGELOG und Git-Tag synchron gehalten werden.

### Removed
- Button „Aktuelle Werte als Preset speichern" und Select „Preset-Speicherziel".
  Der Profil-Editor bearbeitet jeden Slot direkt; das separate Speichern der
  aktiven Werte in einen Slot ist damit überflüssig.

## [0.5.1] – 2026-08-04

### Changed
- Diagnose-Sensoren **Modellnummer** und **Firmwareversion** pollen wieder im
  1-s-Takt (vorher `skip_updates: 59` ≈ 60 s), damit sie nach dem Boot ohne
  Minutenverzögerung erscheinen.

### Removed
- **WiFi-Status-Sync** (Schreiben des Verbindungszustands in Register `0x001E`,
  inkl. `on_boot`-/`wifi`-Trigger, Sync-Script und 15-s-Heartbeat). Das
  Schreiben in dieses undokumentierte Register löste am XY-6509 unerwünschtes
  Verhalten aus (Ausgang schaltete nach dem Einschalten wieder ab).

## [0.5.0] – 2026-08-04

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

## [0.4.0] – 2026-08-04

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

## [0.3.0] – 2026-08-04

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

## [0.2.0] – 2026-08-04

Sicherheit, Recovery und Dokumentation.

### Added
- WPA2-**Fallback-AP** + Captive Portal als Rettungszugang.
- README mit Installationsanleitung (web.esphome.io), Pinout-Bild und
  Verdrahtungstabelle.
- Zwei Konfigurationsvarianten (mit/ohne API-Verschlüsselung).
  _(Die Variante ohne Verschlüsselung wurde in 0.5.0 wieder entfernt.)_

### Security
- Native-**API-Verschlüsselung**, **OTA-Passwort**, `min_auth_mode: WPA2`.

## [0.1.0] – 2026-08-04

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

[Unreleased]: https://github.com/DeadMonkey428/Sinilink-XY-WFPOW-to-XY-6509-ESPHome/compare/v0.5.6...HEAD
[0.5.6]: https://github.com/DeadMonkey428/Sinilink-XY-WFPOW-to-XY-6509-ESPHome/compare/v0.5.5...v0.5.6
[0.5.5]: https://github.com/DeadMonkey428/Sinilink-XY-WFPOW-to-XY-6509-ESPHome/compare/v0.5.4...v0.5.5
[0.5.4]: https://github.com/DeadMonkey428/Sinilink-XY-WFPOW-to-XY-6509-ESPHome/compare/v0.5.3...v0.5.4
[0.5.3]: https://github.com/DeadMonkey428/Sinilink-XY-WFPOW-to-XY-6509-ESPHome/compare/v0.5.2...v0.5.3
[0.5.2]: https://github.com/DeadMonkey428/Sinilink-XY-WFPOW-to-XY-6509-ESPHome/compare/v0.5.1...v0.5.2
[0.5.1]: https://github.com/DeadMonkey428/Sinilink-XY-WFPOW-to-XY-6509-ESPHome/compare/v0.5.0...v0.5.1
[0.5.0]: https://github.com/DeadMonkey428/Sinilink-XY-WFPOW-to-XY-6509-ESPHome/releases/tag/v0.5.0
