# Sinilink XY-WFPOW → XY-6509 (ESPHome)

ESPHome-Firmware für das WiFi-Modul **Sinilink XY-WFPOW** (ESP8285), das per
**Modbus RTU** das Labornetzteil / den DC-DC-Wandler **XY-6509** steuert und
ausliest. Damit hängt das XY-6509 vollständig in **Home Assistant** – inkl.
Messwerten, Sollwerten, Schutzgrenzen, Alarmquittierung und Presets.

Das XY-WFPOW ersetzt dabei seine Original-Firmware; die serielle Hardware-UART
(GPIO1/GPIO3) wird für Modbus zum XY-6509 verwendet.

## Funktionen

**Messwerte** – Ausgangsspannung/-strom/-leistung, Eingangsspannung,
Ausgangskapazität & -energie, Laufzeit, interne & externe Temperatur,
Modell- und Firmwareversion.

**Steuerung** – Sollspannung, Strombegrenzung, Ausgang ein/aus,
Auto-Einschalten nach Gerätestart, Displayhelligkeit, Preset M0–M9.

**Schutzfunktionen** – Grenzwerte für Über-/Unterspannung (OVP/LVP),
Überstrom (OCP), Überleistung (OPP), Kapazität (OAH), Energie (OWH),
Laufzeit (OHP), interne/externe Temperatur (OTP/ETP). Die 0-abschaltbaren
Schutzfunktionen (OAH/OWH/OHP/ETP) haben zusätzlich einen Ein/Aus-Schalter,
der den zuletzt gesetzten Grenzwert merkt und wiederherstellt.

**Status** – Schutzstatus als Klartext, „Schutzabschaltung aktiv“-Sensor,
Tastensperre, Konstantstrom-Anzeige, Button „Alarm quittieren“.

**WiFi-Status auf dem XY-6509-Display** – der Verbindungszustand von ESPHome
wird ins Statusregister des XY-6509 geschrieben, damit dessen WiFi-Symbol
stimmt.

## Zwei Varianten

Zwei eigenständige, vollständige Konfigurationen – einfach die passende
kopieren:

| Datei | API |
|-------|-----|
| [`esphome.yaml`](esphome.yaml) | **mit** Verschlüsselung (empfohlen für Home Assistant) |
| [`esphome-no-encryption.yaml`](esphome-no-encryption.yaml) | **ohne** Verschlüsselung |

Beide sind identisch bis auf den `api:`-Block. Immer nur **eine** davon auf
das Gerät flashen (beide tragen denselben Gerätenamen `sinilink`).

## Secrets

`secrets.yaml` ist bewusst **nicht** im Repo (siehe `.gitignore`). Lege sie
neben der Konfigurationsdatei ab:

```yaml
# secrets.yaml
wifi_ssid: "DeinWLAN"
wifi_password: "..."

# WPA2-Fallback-AP (Rettungszugang, wenn die Station nicht kommt)
ap_password: "mind. 8 Zeichen"

# OTA-Schutz
ota_password: "..."

# Nur für esphome.yaml (verschlüsselte Variante) nötig.
# 32-Byte-Base64-Key, z.B. via `openssl rand -base64 32`
api_encryption_key: "..."
```

Die Variante ohne Verschlüsselung braucht `api_encryption_key` nicht.

## Installation – Schritt für Schritt

**Voraussetzungen:** Chrome oder Edge (für WebSerial), ein USB-Seriell-Adapter
(3,3 V!), Home Assistant mit dem Add-on „ESPHome Device Builder“.

### 1. Erstflash via web.esphome.io

Das XY-WFPOW ist ein nacktes ESP8285-Modul ohne USB – der Erstflash läuft
über einen Seriell-Adapter (**3,3 V!**) an der unteren Stiftleiste.

![Pinbelegung Sinilink XY-WFPOW](doc/images/sinilink_XY-WFPOW_pinout.jpg)

Verdrahtung an der **unteren Stiftleiste** (Adapter ↔ Modul):

| Adapter | Modul |
|---------|-------|
| GND     | GND |
| TX      | GPIO3 / RXD |
| RX      | GPIO1 / TXD |
| 3,3 V   | 3V3 |

Für den **Flash-Modus** zusätzlich **IO0 beim Einschalten auf GND** ziehen
(danach GND wieder lösen).

1. [web.esphome.io](https://web.esphome.io) öffnen → **Connect** → seriellen
   Port wählen.
2. **„Prepare for first use“** – flasht eine generische ESPHome-Firmware.

> Die 4-polige JST-Buchse links (5 V In / RXD / TXD / GND) ist für den
> Betrieb am XY-6509 – zum Flashen die untere Stiftleiste mit **3,3 V** nutzen,
> nicht die 5-V-Buchse.

### 2. WLAN konfigurieren

Direkt nach dem Flashen bietet web.esphome.io an, das Gerät ins WLAN zu
bringen → **SSID und Passwort eingeben**. Das Gerät ist danach im Netzwerk.

### 3. In Home Assistant hinzufügen

Home Assistant erkennt das ESPHome-Gerät automatisch:
**Einstellungen → Geräte & Dienste** → entdecktes Gerät **hinzufügen**.

### 4. Eigene Konfiguration (YAML) einspielen

1. Im **ESPHome-Dashboard** das Gerät **übernehmen** („Adopt“ / „Take control“).
2. **Edit** öffnen und den **gesamten** Inhalt durch
   [`esphome.yaml`](esphome.yaml) (bzw.
   [`esphome-no-encryption.yaml`](esphome-no-encryption.yaml)) ersetzen.
3. `secrets.yaml` im Dashboard anlegen/ergänzen (siehe [Secrets](#secrets)) –
   **wichtig:** `wifi_ssid`/`wifi_password` müssen zum WLAN aus Schritt 2
   passen, sonst fällt das Gerät nach dem Update aus dem Netz.
4. **Install → Wirelessly (OTA)** – die fertige Firmware wird über WLAN
   aufgespielt. Ab jetzt laufen alle weiteren Updates per OTA, kein Adapter
   mehr nötig.

> Bei der **verschlüsselten** Variante fragt Home Assistant beim Übernehmen
> den `api_encryption_key` ab (Wert aus `secrets.yaml`).

### Alternative: ESPHome-CLI

Wer die [ESPHome-CLI](https://esphome.io/guides/cli.html) nutzt, kompiliert und
flasht direkt:

```bash
esphome run esphome.yaml                 # mit Verschlüsselung
esphome run esphome-no-encryption.yaml   # ohne Verschlüsselung
```

## Hinweise

- **Kein Serial-Log:** `logger: baud_rate: 0`, weil die UART0 (GPIO1/GPIO3)
  für Modbus belegt ist. Debugging läuft über die ESPHome-API-Logs.
- **Rettungs-AP:** Bei fehlgeschlagener Station-Verbindung öffnet das Gerät
  den AP „Sinilink Fallback“ + Captive Portal. Ohne diesen wäre das Gerät
  nach einem misslungenen OTA nur per Neu-Flashen erreichbar.
- **Erste Adoption mit Verschlüsselung:** Nach dem Umstieg auf die
  verschlüsselte Variante muss das Gerät in Home Assistant einmal neu
  adoptiert werden (API-Key wird abgefragt). Das ist normal.

## Hardware

- **Sinilink XY-WFPOW** – WiFi-Modul (ESP8285), Board-Profil `esp01_1m`.
- **XY-6509** – DC-DC-Wandler / Netzteil mit Modbus-RTU-Schnittstelle
  (Adresse `0x01`, 115200 Baud, 8N1).
