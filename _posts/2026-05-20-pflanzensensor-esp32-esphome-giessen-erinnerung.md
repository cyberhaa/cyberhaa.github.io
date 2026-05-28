---
layout: post
title: "Nie wieder Pflanzen vergessen: DIY Gieß-Erinnerung mit ESP32 und Home Assistant"
date: 2026-05-20
categories: [Smart Home]
tags: [ESPHome, ESP32, HomeAssistant]
description: "Kapazitiver Bodenfeuchtesensor am ESP32 mit ESPHome – automatische Gieß-Erinnerung per Push-Nachricht wenn die Pflanze Durst hat."
image:
  path: /assets/img/posts/esp-soil-sensor.jpg
---

Ich bin ein Pflanzen-Killer. Nicht absichtlich – ich vergesse es einfach. Die Monstera im Wohnzimmer sieht drei Wochen lang prächtig aus und dann hängen plötzlich alle Blätter. Bis ich es merke, ist es fast zu spät.

Die Lösung: Ein [ESP32 *]({{ site.data.amazon.esp32_c3_supermini.url }}){:target="_blank"} mit einem [Bodenfeuchtesensor *]({{ site.data.amazon.kapazitiver_bodensensor.url }}){:target="_blank"} in der Erde, der mir eine Push-Nachricht aufs Handy schickt wenn die Pflanze Durst hat. Kosten: unter 8€. Bauzeit: 15 Minuten.

![Kapazitiver Bodenfeuchtesensor am ESP32 im Blumentopf](/assets/img/posts/esp-soil-sensor.jpg){: .shadow .rounded-10 }
_Der fertige DIY-Pflanzensensor im Einsatz_

## Warum kapazitiv und nicht resistiv?

Es gibt zwei Arten von Bodenfeuchtesensoren:
- **Resistiv** (die billigen mit zwei blanken Metallstiften): Messen den Widerstand zwischen den Stiften. Problem: Die korrodieren innerhalb von Wochen in feuchter Erde. Nach einem Monat sind sie Schrott.
- **Kapazitiv** (die mit der vergossenen Platine): Messen die Kapazitätsänderung durch Feuchtigkeit. Kein blankes Metall in der Erde = keine Korrosion. Halten Jahre.

Nimm den kapazitiven. Die 2€ Aufpreis lohnen sich.

## Hardware

- [ESP32-C3 Super Mini *]({{ site.data.amazon.esp32_c3_supermini.url }}){:target="_blank"} (~4€)
- [Kapazitiver Bodenfeuchtesensor v1.2 *]({{ site.data.amazon.kapazitiver_bodensensor.url }}){:target="_blank"} (~3€)
- [Dupont-Kabel *]({{ site.data.amazon.dupont_kabel.url }}){:target="_blank"} (3 Stück)
- USB-C Netzteil (altes Handy-Ladegerät)

## Verkabelung

Drei Kabel, fertig:

| Sensor Pin | ESP32-C3 Pin |
|-----------|-------------|
| VCC | 3.3V |
| GND | GND |
| AOUT | GPIO0 |

Den Sensor steckst du bis zur Markierung in die Erde (nicht tiefer – die Elektronik oben darf nicht nass werden). Den ESP32 legst du neben den Topf oder klebst ihn mit doppelseitigem Klebeband an die Rückseite.

## ESPHome Config

```yaml
esphome:
  name: pflanze-monstera

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
  - platform: adc
    pin: GPIO0
    name: "Monstera Bodenfeuchtigkeit"
    id: bodenfeuchtigkeit
    update_interval: 300s
    unit_of_measurement: "%"
    accuracy_decimals: 0
    attenuation: 11db
    filters:
      - calibrate_linear:
          - 2.75 -> 0
          - 1.25 -> 100
      - lambda: |-
          if (x < 0) return 0;
          if (x > 100) return 100;
          return x;
```

**Update-Intervall:** Alle 5 Minuten (300s) reicht völlig. Erde trocknet nicht in Sekunden aus, und der ESP32 braucht so weniger Strom.

## Kalibrierung: Einmal machen, dann passt es

Die Werte `2.75` und `1.25` in der Config sind Startwerte – die musst du für deinen Sensor anpassen:

1. **Sensor in die Luft halten** → In den ESPHome-Logs den Rohwert ablesen (z.B. 2.8V) → Das ist dein 0%-Wert
2. **Sensor in ein Glas Wasser stecken** (bis zur Markierung) → Rohwert ablesen (z.B. 1.2V) → Das ist dein 100%-Wert
3. Diese zwei Werte in `calibrate_linear` eintragen

Danach zeigt der Sensor in Home Assistant einen sauberen Prozentwert an: 0% = staubtrocken, 100% = klatschnass.

## Home Assistant: Gieß-Erinnerung

Jetzt der eigentliche Sinn des Ganzen – die automatische Benachrichtigung:

```yaml
{% raw %}
automation:
  - alias: "Monstera hat Durst"
    trigger:
      - platform: numeric_state
        entity_id: sensor.monstera_bodenfeuchtigkeit
        below: 30
        for: "01:00:00"
    action:
      - service: notify.mobile_app
        data:
          title: "🌱 Monstera gießen!"
          message: "Bodenfeuchtigkeit bei {{ states('sensor.monstera_bodenfeuchtigkeit') }}%. Zeit zum Gießen."
{% endraw %}
```

**Warum `for: 01:00:00`?** Damit du nicht sofort eine Nachricht bekommst wenn der Wert kurz unter 30% fällt (z.B. weil der Sensor beim Umtopfen kurz in der Luft war). Erst wenn die Erde eine Stunde lang unter 30% ist, kommt die Erinnerung.

## Mehrere Pflanzen? Kein Problem

Du kannst mehrere Sensoren an einen ESP32 hängen. Der ESP32-C3 hat mehrere ADC-Pins:

```yaml
sensor:
  - platform: adc
    pin: GPIO0
    name: "Monstera Bodenfeuchtigkeit"
    # ... (Config wie oben)

  - platform: adc
    pin: GPIO1
    name: "Ficus Bodenfeuchtigkeit"
    # ... (gleiche Filter, andere Kalibrierung)

  - platform: adc
    pin: GPIO2
    name: "Basilikum Bodenfeuchtigkeit"
    # ...
```

Ein ESP32 für 3-4 Pflanzen, ein USB-Netzteil, fertig. Ich habe einen im Wohnzimmer (Monstera + Ficus) und einen in der Küche (Kräuter).

## Dashboard: Alle Pflanzen auf einen Blick

In Home Assistant kannst du dir eine schöne Übersicht bauen:
- **Gauge-Karte** pro Pflanze (grün = feucht, rot = trocken)
- **History-Graph** um zu sehen wie schnell die Erde austrocknet
- **Conditional Card** die nur angezeigt wird wenn eine Pflanze unter 30% ist

Aus dem History-Graph lernst du auch, wie oft welche Pflanze gegossen werden muss. Meine Monstera braucht alle 10 Tage Wasser, der Basilikum alle 3 Tage.

## Tipps aus der Praxis

- **Sensor-Position:** Steck den Sensor nicht direkt neben den Stamm, sondern etwas weiter außen. Dort trocknet die Erde zuerst aus.
- **Schwellenwert:** 30% ist ein guter Startwert für die meisten Zimmerpflanzen. Sukkulenten mögen es trockener (20%), Farne feuchter (50%).
- **Gehäuse:** Druck dir ein kleines [Gehäuse für den ESP32]({% post_url 2026-05-18-3d-drucker-upgrades-die-sich-lohnen %}) – sieht besser aus als ein nacktes Board neben dem Blumentopf.
- **Kabel verstecken:** Das USB-Kabel hinter dem Topf entlang zur nächsten Steckdose führen. Oder eine Steckdosenleiste hinter der Pflanze verstecken.

## Fazit

8€ und 15 Minuten Arbeit – und ich habe seit Monaten keine Pflanze mehr vertrocknen lassen. Die Push-Nachricht kommt genau dann wenn es nötig ist, nicht zu früh und nicht zu spät. Und das History-Diagramm in Home Assistant zeigt mir über die Zeit, welche Pflanze wie viel Wasser braucht.

Das ist für mich Smart Home in seiner besten Form: Ein echtes Problem lösen, für fast kein Geld, komplett lokal.

*Die Grundlagen zu ESPHome und weitere Sensor-Projekte findest du in meinem [ESPHome Einsteiger-Artikel]({% post_url 2026-04-19-esphome-sensoren-einsteiger %}). Und den ESP32-C3 Super Mini kennst du vielleicht schon aus dem [Neato-Saugroboter-Projekt]({% post_url 2026-03-15-neato-botvac-d5-esp32-home-assistant %}).*

