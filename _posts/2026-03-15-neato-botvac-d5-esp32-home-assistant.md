---
layout: post
title: "Neato Botvac D5 retten: ESP32 Bridge für Home Assistant (nach Cloud-Abschaltung)"
date: 2026-03-15
categories: [Smart Home]
tags: [ESPHome, HomeAssistant, ESP32]
description: "Neato hat die Cloud abgeschaltet – dein Botvac D5 ist jetzt dumm. Mit einem ESP32 und ESPHome steuerst du ihn wieder komplett über Home Assistant."
image:
  path: /assets/img/posts/neato-vorne.jpeg
---

Wenn du einen Neato Botvac D5 (oder D3, D4, D6, D7) besitzt, hast du es vermutlich schon mitbekommen: **Neato hat die Cloud-Server abgeschaltet.** Die App? Tot. Die Home Assistant Cloud-Integration? Auch tot. Dein 400-Euro-Saugroboter ist jetzt im Grunde ein teurer Briefbeschwerer mit einem einzigen Knopf oben drauf.

Wegwerfen? Kommt nicht in die Tüte. Der Roboter selbst funktioniert ja noch einwandfrei – nur die Verbindung zur Außenwelt fehlt. Und genau die bauen wir jetzt selbst. Mit einem [ESP32-C3 Super Mini *]({{ site.data.amazon.esp32_devkit.url }}){:target="_blank"} für unter 5 Euro und ESPHome.

![Neato Botvac D5 Saugroboter von vorne nach Cloud-Abschaltung](/assets/img/posts/neato-vorne.jpeg){: .shadow .rounded-10 }
_Mein Neato Botvac D5 – äußerlich top, nur die Cloud ist weg_

## Die Idee: Serial-to-WiFi Bridge

Was die wenigsten wissen: Der Neato hat einen offenen Debug-Port am Bumper. Darüber kann man ihm ganz stumpf Textbefehle schicken – "Clean House", "GoToCharger", "GetCharger" und so weiter. Das ist quasi eine eingebaute Kommandozeile, die Neato vermutlich für die Entwicklung drin gelassen hat.

Unser Plan:
1. Einen ESP32-C3 Super Mini an diesen Debug-Port anschließen (3 Kabel)
2. ESPHome auf den ESP32 flashen
3. Über ESPHome die seriellen Befehle als Buttons in Home Assistant verfügbar machen

Kein Löten an der Neato-Platine nötig, nur Dupont-Stecker aufstecken.

## Hardware: Was du brauchst

- [ESP32-C3 Super Mini *]({{ site.data.amazon.esp32_devkit.url }}){:target="_blank"} (winzig, günstig, reicht völlig)
- [Dupont-Kabel (Female-to-Female) *]({{ site.data.amazon.dupont_kabel.url }}){:target="_blank"} – 3 Stück reichen
- Doppelseitiges Klebeband für die Montage
- Optional: Kleiner Kondensator (100µF) zur Stabilisierung

## Schritt 1: Den Debug-Port finden

Beim D5 (und allen Gen3-Modellen) sitzt der Debug-Port **vorne am Bumper**. Bumper vorsichtig abnehmen – da ist ein 4-Pin Stecker. Belegung von links nach rechts:

| Pin | Funktion |
|-----|----------|
| 1   | RX (Neato empfängt) |
| 2   | 3.3V |
| 3   | TX (Neato sendet) |
| 4   | GND |

![Neato D5 Debug-Port am Bumper mit 4-Pin Stecker für ESP32 Anschluss](/assets/img/posts/neato-anschluss.jpeg){: .shadow .rounded-10 }
_Der 4-Pin Debug-Port am Bumper – hier stecken wir den ESP32 an_

## Schritt 2: Verkabelung

Drei Kabel, das war's:

| Neato Pin | ESP32-C3 Pin | Warum |
|-----------|-------------|-------|
| Pin 1 (RX) | GPIO 6 (TX) | ESP sendet → Neato empfängt |
| Pin 3 (TX) | GPIO 7 (RX) | Neato sendet → ESP empfängt |
| Pin 4 (GND) | GND | Gemeinsame Masse (Pflicht!) |

**Wichtig – Fallstricke beim ESP32-C3 Super Mini:**
- **GPIO 20/21 NICHT verwenden** – die sind für USB-CDC reserviert und killen das WiFi
- **GPIO 4/5 NICHT verwenden** – JTAG-Pins, verursachen Probleme
- **GPIO 6/7 sind die richtigen** für UART
- **Niemals USB und Neato-3.3V gleichzeitig anschließen!** Entweder USB oder Neato-Strom, nicht beides

GND muss immer verbunden sein, auch wenn der ESP per USB versorgt wird. Ohne gemeinsame Masse funktioniert die serielle Kommunikation nicht.

![ESP32-C3 Super Mini mit Kondensator zur Spannungsstabilisierung im Neato](/assets/img/posts/esp-neato-kondensator.jpeg){: .shadow .rounded-10 }
_Der ESP32-C3 Super Mini mit Stabilisierungs-Kondensator, bereit für den Einbau_

Befestigt den ESP32 mit doppelseitigem Klebeband im Roboter. Lasst die Kabel lang genug, damit sich der Bumper noch abnehmen lässt.

## Schritt 3: ESPHome konfigurieren

Öffne ESPHome in Home Assistant (falls noch nicht installiert: als Add-on im Add-on Store). Leg ein neues Gerät an und nutz diese Config:

```yaml
esphome:
  name: neato-bridge
  platformio_options:
    board_build.flash_mode: dio

esp32:
  board: esp32-c3-devkitm-1
  variant: ESP32C3
  framework:
    type: arduino

wifi:
  ssid: "EUER_WLAN"
  password: "EUER_PASSWORT"
  power_save_mode: none
  fast_connect: true
  reboot_timeout: 15min
  output_power: 8.5dB
  ap:
    ssid: "Neato-Fallback"
    password: "neato12345"

captive_portal:

api:
  encryption:
    key: "generiert-esphome-einen-key"

ota:
  - platform: esphome

logger:
  baud_rate: 0
  level: DEBUG

uart:
  id: neato_uart
  tx_pin: GPIO6
  rx_pin: GPIO7
  baud_rate: 115200
  rx_buffer_size: 2048

button:
  - platform: uart
    uart_id: neato_uart
    name: "Neato Haus saugen"
    data: "Clean House\n"

  - platform: uart
    uart_id: neato_uart
    name: "Neato Spot saugen"
    data: "Clean Spot\n"

  - platform: uart
    uart_id: neato_uart
    name: "Neato Stop"
    data: "Clean Stop\n"

  - platform: uart
    uart_id: neato_uart
    name: "Neato zur Ladestation"
    data: "GoToCharger\n"

  - platform: uart
    uart_id: neato_uart
    name: "Neato Pause"
    data: "Clean Pause\n"

  - platform: uart
    uart_id: neato_uart
    name: "Neato Resume"
    data: "Clean Resume\n"

  - platform: uart
    uart_id: neato_uart
    name: "Neato finden (Piep)"
    data: "PlaySound SoundId 20 \n"

  - platform: uart
    uart_id: neato_uart
    name: "Neato Akku"
    data: "GetCharger \n"

  - platform: uart
    uart_id: neato_uart
    name: "Neato Fehler löschen"
    data: "SetUIError clearall \n"
```

**Warum die ganzen WiFi-Parameter?** Der ESP32-C3 Super Mini hat ein bekanntes Problem: Wenn UART aktiv ist, bricht das WiFi gerne ab ("Auth Expired"). Die Kombination aus `power_save_mode: none`, `fast_connect: true` und reduzierter `output_power` behebt das. Hat mich einen ganzen Abend Fehlersuche gekostet, bis ich das rausgefunden hatte.

**Wichtig:** Gen3-Neatos (D3-D7) erwarten Befehle mit `\n` am Ende – nicht `\r\n` wie die älteren Modelle!

## Schritt 4: Flashen

Der ESP32-C3 Super Mini hat keinen separaten USB-Chip, sondern nutzt den internen USB-CDC. Das heißt: Beim ersten Mal musst du ihn in den Download-Modus bringen:

1. **BOOT-Knopf gedrückt halten** → USB einstecken → 2 Sekunden warten → BOOT loslassen
2. In ESPHome auf "Install" → "Plug into this computer" klicken
3. Nach dem ersten Flash verbindet sich der ESP32 mit eurem WLAN
4. Ab jetzt laufen Updates Over-the-Air (kein USB mehr nötig)

Alternativ geht auch [web.esphome.io](https://web.esphome.io) direkt im Chrome-Browser.

## Schritt 5: Testen

Steck die Dupont-Kabel an den Neato Debug-Port, schaltet den Neato ein (nicht nur Standby – er muss richtig an sein) und schau in Home Assistant unter **Einstellungen → Geräte & Dienste**. Der ESP32 sollte automatisch erkannt werden.

Drück auf "Neato finden (Piep)" – wenn der Roboter piept, steht die Verbindung. Dann drückt auf "Neato Haus saugen" und der totgeglaubte Saugroboter springt an. Ein ziemlich geiles Gefühl, muss ich sagen.

## Troubleshooting

Falls es nicht auf Anhieb klappt – hier die häufigsten Probleme:

- **ESP32 verbindet sich nicht mit WiFi:** GPIO 20/21 als UART-Pins verwendet? Das killt WiFi. Auf GPIO 6/7 wechseln.
- **Neato reagiert nicht auf Befehle:** TX und RX vertauscht? TX vom Neato muss an RX vom ESP (und umgekehrt). Auch prüfen: Ist GND verbunden?
- **ESP32 zeigt im Log nur Echos:** Defektes Dupont-Kabel, das TX und RX kurzschließt. Andere Kabel probieren.
- **Rote (i) Lampe blinkt am Neato:** Bumper nicht richtig eingerastet oder Staubbehälter fehlt. "Neato Fehler löschen" Button drücken.

## Bonus: Weitere Befehle

Der Neato versteht noch deutlich mehr. Hier die nützlichsten:

| Befehl | Funktion |
|--------|----------|
| `GetCharger \n` | Akkustand, Spannung, Strom |
| `GetState \n` | Aktueller Zustand (saugend, ladend, Fehler) |
| `GetErr \n` | Fehlercodes auslesen |
| `GetVersion \n` | Firmware-Version, Seriennummer |
| `TestMode On \n` | Erweiterte Befehle freischalten |
| `SetSystemMode Shutdown \n` | Herunterfahren (TestMode muss On sein) |

## Home Assistant Automatisierung

Jetzt wo der Neato wieder steuerbar ist, kannst du ihn in deine Smart-Home-Logik einbauen:

```yaml
automation:
  - alias: "Neato saugt wenn niemand da ist"
    trigger:
      - platform: state
        entity_id: group.all_persons
        to: "not_home"
        for: "00:10:00"
    condition:
      - condition: time
        after: "09:00:00"
        before: "20:00:00"
    action:
      - service: button.press
        target:
          entity_id: button.neato_haus_saugen
```

## Fazit

Für unter 5 Euro und einen Abend Bastelei hast du deinen Neato komplett wiederbelebt – ohne Cloud, ohne App, ohne Abo. Alles läuft lokal über euer eigenes Netzwerk. So wie es sein sollte.

Und das Projekt zeigt mal wieder, warum ein [lokaler Home Assistant Server]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) Gold wert ist: Wenn ein Hersteller seine Cloud abschaltet, baut man sich die Verbindung halt selbst. Tschüss, geplante Obsoleszenz.

*Wer tiefer einsteigen will: Das [neato-brainslug Projekt auf GitHub](https://github.com/Philip2809/neato-brainslug) bietet eine vollständige Gen3-Integration mit Status-Parsing, Akku-Sensoren und einer echten Vacuum-Entity in Home Assistant.*

<style>
  .preview-img { display: none !important; }
</style>
