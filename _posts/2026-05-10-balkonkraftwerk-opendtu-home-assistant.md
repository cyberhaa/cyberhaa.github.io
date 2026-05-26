---
layout: post
title: "Balkonkraftwerk mit OpenDTU in Home Assistant: Live-Produktion ohne Cloud"
date: 2026-05-10
categories: [Smart Home]
tags: [Solar, HomeAssistant, ESP32]
description: "Balkonkraftwerk mit OpenDTU und Home Assistant überwachen – Live-Produktion per MQTT, ohne Hoymiles Cloud, komplett lokal."
image:
  path: /assets/img/posts/opendtu-balkonkraftwerk.jpg
---

Seit der Gesetzesänderung darf ein Balkonkraftwerk in Deutschland bis zu 800 Watt ins Hausnetz einspeisen. Einstecken, anmelden, fertig. Aber dann? Die Hoymiles-App zeigt zwar irgendwas an, aber die Daten laufen über chinesische Cloud-Server, die Aktualisierung dauert ewig, und in Home Assistant siehst du erstmal gar nichts.

Bei mir hängt das [Balkonkraftwerk *]({{ site.data.amazon.balkonkraftwerk_800w.url }}){:target="_blank"} am Balkongeländer mit einem [Hoymiles Wechselrichter *]({{ site.data.amazon.hoymiles_hms_800.url }}){:target="_blank"}. Um die Live-Produktion lokal und ohne Cloud in Home Assistant zu bekommen, nutze ich **OpenDTU** – eine Open-Source-Firmware auf einem ESP32, die direkt per Funk mit dem Wechselrichter spricht.

## Was ist OpenDTU?

OpenDTU ist ein Ersatz für die originale Hoymiles DTU (Data Transfer Unit). Statt die Daten in die s-Miles Cloud zu schicken, liest der ESP32 die Produktionsdaten per Funk direkt vom Wechselrichter aus und stellt sie lokal bereit – per Webinterface und per MQTT.

Der Vorteil gegenüber der Hoymiles-App:
- **Echtzeit-Daten** – Aktualisierung alle paar Sekunden statt alle 15 Minuten
- **Keine Cloud** – Alles bleibt in deinem Netzwerk
- **MQTT** – Perfekte Integration in Home Assistant
- **Kostenlos** – Open Source, ESP32 kostet unter 10€

## Hardware: Was du brauchst

- [OpenDTU-Board (ESP32 + NRF24L01+) *]({{ site.data.amazon.opendtu_board.url }}){:target="_blank"} – es gibt fertige Boards, auf denen alles schon verlötet ist
- Alternativ: Einen ESP32 + ein NRF24L01+ Funkmodul (für HM-Serie) oder CMT2300A (für HMS-Serie) selbst zusammenstecken
- USB-Netzteil zur Stromversorgung

**Welches Funkmodul brauche ich?**
- Hoymiles **HM**-Serie (HM-300, HM-600, HM-800): **NRF24L01+** Modul
- Hoymiles **HMS**-Serie (HMS-800W-2T etc.): **CMT2300A** Modul

Am einfachsten ist ein fertiges OpenDTU-Board, da ist alles drauf und du musst nur noch die Firmware flashen.

## Schritt 1: OpenDTU flashen

1. Geh auf [opendtu.solar](https://www.opendtu.solar/){:target="_blank"} und lad die aktuelle Firmware herunter
2. Verbinde den ESP32 per USB mit deinem Rechner
3. Öffne [install.opendtu.solar](https://install.opendtu.solar/){:target="_blank"} im Chrome-Browser
4. Klick auf "Connect" → wähle den USB-Port → Flash

Das war's. Nach dem Flash öffnet der ESP32 ein eigenes WLAN mit dem Namen "OpenDTU-*". Verbinde dich damit (Passwort: `openDTU42`) und ruf `http://192.168.4.1` im Browser auf.

## Schritt 2: OpenDTU konfigurieren

Im Webinterface trägst du zuerst dein WLAN ein, damit sich OpenDTU mit deinem Heimnetz verbindet. Nach dem Neustart findest du das Gerät unter seiner neuen IP-Adresse (schau in deinem Router nach).

Dann den Wechselrichter hinzufügen:
1. **Settings → Inverter** → "Add Inverter"
2. Seriennummer deines Hoymiles eingeben (steht auf dem Aufkleber am Wechselrichter)
3. Namen vergeben (z.B. "Balkon")
4. Speichern

Wenn alles stimmt, siehst du nach wenigen Sekunden die Live-Daten: Aktuelle Leistung pro Panel, Tagesertrag, Spannung, Strom, Temperatur des Wechselrichters.

## Schritt 3: MQTT einrichten (für Home Assistant)

Damit die Daten in Home Assistant landen, aktivieren wir MQTT:

1. **Settings → MQTT** im OpenDTU-Webinterface
2. Broker-IP: Die IP deines MQTT-Servers (z.B. Mosquitto auf Unraid)
3. Port: 1883
4. Username/Passwort: Deine MQTT-Zugangsdaten
5. Topic: `solar/` (oder was du willst)
6. "Enable MQTT" aktivieren und speichern

OpenDTU schickt jetzt alle paar Sekunden die aktuellen Werte an deinen MQTT-Broker.

## Schritt 4: Home Assistant Integration

In Home Assistant brauchst du die **MQTT-Integration** (falls noch nicht eingerichtet: Einstellungen → Geräte & Dienste → MQTT hinzufügen).

OpenDTU unterstützt **MQTT Auto-Discovery** – das heißt: Sobald die MQTT-Verbindung steht, tauchen die Sensoren automatisch in Home Assistant auf. Kein manuelles YAML nötig.

Du findest dann unter Geräte einen neuen Eintrag "OpenDTU" mit Sensoren wie:
- **Aktuelle Leistung (W)** – was gerade vom Balkon kommt
- **Tagesertrag (Wh)** – was heute schon produziert wurde
- **Gesamtertrag (kWh)** – seit Installation
- **Spannung und Strom** – pro Panel einzeln
- **Wechselrichter-Temperatur**

## Schritt 5: Energy Dashboard einrichten

Jetzt kommt der schöne Teil. Geh in Home Assistant auf **Einstellungen → Dashboards → Energie**.

Unter "Solarproduktion" fügst du den Sensor für den Gesamtertrag hinzu (der Sensor, der in kWh zählt). Home Assistant rechnet daraus automatisch die tägliche, wöchentliche und monatliche Produktion.

In Kombination mit meinem [Smart Meter Lesekopf]({% post_url 2025-08-11-smartmeter-hichi-in-homeassistant %}) sehe ich jetzt auf einen Blick:
- Wie viel der Balkon gerade produziert
- Wie viel das Haus gerade verbraucht
- Ob ich gerade Strom einspeise oder zukaufe

## Bonus: Automatisierung bei Solar-Überschuss

Wenn die Sonne knallt und der Wechselrichter mehr liefert als das Haus braucht, kann Home Assistant automatisch Verbraucher einschalten:

```yaml
automation:
  - alias: "Waschmaschine bei Solar-Überschuss"
    trigger:
      - platform: numeric_state
        entity_id: sensor.stromzaehler_aktuelle_leistung
        below: -200
        for: "00:05:00"
    action:
      - service: notify.mobile_app
        data:
          title: "Solar-Überschuss!"
          message: "Über 200W Überschuss seit 5 Minuten. Guter Zeitpunkt für die Waschmaschine."
```

Klar, die Waschmaschine schaltet sich nicht von alleine ein – aber die Benachrichtigung aufs Handy reicht, um den Strom sinnvoll zu nutzen statt ihn für 8 Cent ins Netz zu verschenken.

## Warum nicht einfach die Hoymiles-App?

Kurze Antwort: Weil ich meine Daten nicht über chinesische Server schicken will, nur um zu sehen wie viel mein Balkon gerade produziert. Und weil die App-Daten nur alle 15 Minuten aktualisiert werden – OpenDTU liefert Echtzeit.

Außerdem: Wenn Hoymiles irgendwann den Cloud-Service einstellt (wie Neato es mit den [Saugrobotern gemacht hat]({% post_url 2026-03-15-neato-botvac-d5-esp32-home-assistant %})), stehst du ohne Daten da. Mit OpenDTU bist du davon komplett unabhängig.

## Fazit

Für unter 20 Euro (ESP32-Board) hast du dein Balkonkraftwerk komplett lokal in Home Assistant eingebunden. Echtzeit-Daten, Energy Dashboard, Automatisierungen bei Überschuss – alles ohne Cloud, alles in deiner Hand.

Das Setup läuft bei mir seit Monaten absolut stabil. Der ESP32 hängt per USB-Netzteil neben dem Wechselrichter am Balkon und funkt fröhlich vor sich hin. Einmal eingerichtet, null Wartung.

*Noch keinen Smart Meter? In meinem Artikel [Stromzähler smart machen]({% post_url 2025-08-11-smartmeter-hichi-in-homeassistant %}) zeige ich dir, wie du den Live-Verbrauch deines Hauses in Home Assistant bekommst – die perfekte Ergänzung zum Balkonkraftwerk.*

<style>
  .preview-img { display: none !important; }
</style>
