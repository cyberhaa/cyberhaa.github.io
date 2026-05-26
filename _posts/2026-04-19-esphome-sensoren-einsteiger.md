---
layout: post
title: "ESPHome für Einsteiger: 3 Sensoren selbst bauen (unter 10€ pro Stück)"
date: 2026-04-19
categories: [Smart Home]
tags: [ESPHome, ESP32, HomeAssistant]
description: "3 ESPHome-Sensoren für Home Assistant selbst bauen – Temperatur, Licht und Bodenfeuchtigkeit. Unter 10€ pro Sensor, kein Löten nötig."
image:
  path: /assets/img/posts/esphome-sensoren.jpg
---

Zigbee-Sensoren sind praktisch – aber manchmal will man was Spezielles, das es fertig nicht gibt. Oder man will einfach nicht 25€ für einen Temperatursensor ausgeben, wenn ein ESP32 mit Sensor-Modul für unter 10€ das Gleiche kann.

Mit **ESPHome** baust du dir in 15 Minuten eigene Sensoren, die direkt in Home Assistant auftauchen. Kein Programmieren nötig – alles läuft über eine YAML-Config. Und das Beste: Die Dinger laufen über WLAN, du brauchst keinen Zigbee-Stick dafür.

Hier sind 3 Projekte für den Einstieg, die ich selbst im Einsatz habe.

## Was du für alle Projekte brauchst

- [ESP32-C3 Super Mini *]({{ site.data.amazon.esp32_c3_supermini.url }}){:target="_blank"} – winzig, günstig (~4€){:target="_blank"}, WiFi eingebaut
- USB-C Kabel zum Flashen (danach OTA über WiFi)
- [Dupont-Kabel *]({{ site.data.amazon.dupont_kabel.url }}){:target="_blank"} zum Verbinden
- ESPHome Add-on in Home Assistant (im Add-on Store installieren)

Die Sensoren steckst du einfach mit Dupont-Kabeln auf den ESP32 – kein Löten nötig für den Anfang. Wenn es dauerhaft sein soll, kannst du später löten.

## Projekt 1: Temperatur & Luftfeuchtigkeit (DHT22)

Der Klassiker. Ein [DHT22-Sensor *]({{ site.data.amazon.dht22_sensor.url }}){:target="_blank"} misst Temperatur und Luftfeuchtigkeit. Perfekt für Räume, den Keller oder draußen (in einem wetterfesten Gehäuse){:target="_blank"}.

### Verkabelung

| DHT22 Pin | ESP32-C3 Pin |
|-----------|-------------|
| VCC (+ ) | 3.3V |
| DATA | GPIO4 |
| GND (-) | GND |

### ESPHome Config

```yaml
esphome:
  name: temp-wohnzimmer

esp32:
  board: esp32-c3-devkitm-1
  variant: ESP32C3
  framework:
    type: arduino

wifi:
  ssid: "DEIN_WLAN"
  password: "DEIN_PASSWORT"

api:
  encryption:
    key: "generiere-einen-key"

logger:

ota:
  - platform: esphome

sensor:
  - platform: dht
    pin: GPIO4
    model: DHT22
    temperature:
      name: "Wohnzimmer Temperatur"
    humidity:
      name: "Wohnzimmer Luftfeuchtigkeit"
    update_interval: 60s
```

Nach dem Flashen taucht der Sensor automatisch in Home Assistant auf. Aktualisierung alle 60 Sekunden, Genauigkeit ±0.5°C. Für 8€ total.

## Projekt 2: Lichtsensor (BH1750)

Ein [BH1750 *]({{ site.data.amazon.bh1750_sensor.url }}){:target="_blank"} misst die Helligkeit in Lux. Damit kannst du Automatisierungen bauen wie "Licht einschalten wenn es draußen dunkel wird" – präziser als nur nach Sonnenuntergang zu gehen (bewölkte Tage sind auch dunkel){:target="_blank"}.

### Verkabelung (I2C)

| BH1750 Pin | ESP32-C3 Pin |
|-----------|-------------|
| VCC | 3.3V |
| GND | GND |
| SDA | GPIO6 |
| SCL | GPIO7 |

### ESPHome Config

```yaml
i2c:
  sda: GPIO6
  scl: GPIO7

sensor:
  - platform: bh1750
    name: "Wohnzimmer Helligkeit"
    address: 0x23
    update_interval: 30s
```

Den Rest (esphome, esp32, wifi, api, ota) übernimmst du aus Projekt 1. In Home Assistant hast du dann einen Lux-Wert, den du als Trigger für Licht-Automatisierungen nutzen kannst.

## Projekt 3: Bodenfeuchtigkeit für Pflanzen

Der [kapazitive Bodenfeuchtesensor *]({{ site.data.amazon.kapazitiver_bodensensor.url }}){:target="_blank"} misst wie feucht die Erde deiner Zimmerpflanze ist. Keine Korrosion (im Gegensatz zu den billigen resistiven Sensoren){:target="_blank"}, hält ewig.

### Verkabelung

| Sensor Pin | ESP32-C3 Pin |
|-----------|-------------|
| VCC | 3.3V |
| GND | GND |
| AOUT | GPIO0 (ADC) |

### ESPHome Config

```yaml
sensor:
  - platform: adc
    pin: GPIO0
    name: "Monstera Bodenfeuchtigkeit"
    update_interval: 600s
    unit_of_measurement: "%"
    attenuation: 11db
    filters:
      - calibrate_linear:
          - 2.8 -> 0    # Sensor in der Luft = 0% feucht
          - 1.3 -> 100  # Sensor in Wasser = 100% feucht
      - lambda: |-
          if (x < 0) return 0;
          if (x > 100) return 100;
          return x;
```

**Kalibrierung:** Die Werte 2.8V (trocken) und 1.3V (nass) musst du für deinen Sensor anpassen. Steck ihn erst in die Luft (notier den Wert), dann in ein Glas Wasser (notier den Wert). Diese zwei Punkte trägst du in `calibrate_linear` ein.

**Automatisierung:** Benachrichtigung aufs Handy wenn die Monstera unter 30% fällt. Nie wieder vergessene Pflanzen.

## Tipps für alle ESPHome-Projekte

- **Stromversorgung:** Für stationäre Sensoren einfach ein altes USB-Handy-Ladegerät nehmen. Der ESP32-C3 zieht im Betrieb ~80mA.
- **Gehäuse:** Auf Printables gibt es hunderte ESP32-Gehäuse zum Drucken. Oder eine kleine Dose aus dem Baumarkt.
- **OTA-Updates:** Nach dem ersten Flash per USB laufen alle Updates Over-the-Air. Du musst den Sensor nie wieder abstecken.
- **Mehrere Sensoren pro ESP32:** Du kannst problemlos DHT22 + BH1750 + Bodensensor an einen einzigen ESP32 hängen. Spart Hardware und Steckdosen.

## Warum ESPHome statt fertige Zigbee-Sensoren?

| | ESPHome (DIY) | Zigbee-Sensor (fertig) |
|---|---|---|
| Kosten | 5-10€ | 15-30€ |
| Flexibilität | Jeder Sensor möglich | Nur was der Hersteller anbietet |
| Batterie | Nein (USB-Strom) | Ja (Knopfzelle) |
| Reichweite | WiFi (überall wo WLAN ist) | Zigbee-Mesh (begrenzt) |
| Einrichtung | 15 Min Config schreiben | 30 Sek pairen |

Für Stellen mit Steckdose in der Nähe ist ESPHome unschlagbar günstig und flexibel. Für batteriebetriebene Sensoren an Fenstern und Türen sind Zigbee-Geräte besser (ein ESP32 an einer Batterie hält nur Stunden, ein Zigbee-Sensor Jahre).

## Fazit

ESPHome ist der perfekte Einstieg in die DIY-Smart-Home-Welt. Für unter 10€ pro Sensor baust du dir genau das, was du brauchst – ohne auf einen Hersteller angewiesen zu sein. Und wenn der Sensor kaputt geht, baust du für ein paar Euro einen neuen.

*Den ESP32-C3 Super Mini kennst du vielleicht schon aus meinem [Neato-Saugroboter-Projekt]({% post_url 2026-03-15-neato-botvac-d5-esp32-home-assistant %}) – das gleiche Board, nur anderer Einsatzzweck.*

<style>
  .preview-img { display: none !important; }
</style>
