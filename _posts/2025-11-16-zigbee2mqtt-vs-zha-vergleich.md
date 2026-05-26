---
layout: post
title: "Zigbee2MQTT vs. ZHA: Welche Zigbee-Integration für Home Assistant?"
date: 2025-11-16
categories: [Smart Home]
tags: [Zigbee, HomeAssistant, Zigbee2MQTT]
description: "Zigbee2MQTT oder ZHA? Ehrlicher Vergleich der beiden Zigbee-Integrationen für Home Assistant – Geräte-Support, Stabilität und Einrichtung."
---

Wer Zigbee-Geräte in Home Assistant einbinden will, steht vor einer grundlegenden Entscheidung: **Zigbee2MQTT (Z2M)** oder **ZHA (Zigbee Home Automation)**? Beide machen im Grunde das Gleiche – Zigbee-Geräte mit Home Assistant verbinden. Aber der Weg dahin und die Möglichkeiten unterscheiden sich deutlich.

Ich habe mit ZHA angefangen, bin nach ein paar Monaten zu Zigbee2MQTT gewechselt und bin seitdem dabei geblieben. Hier ist mein ehrlicher Vergleich.

## ZHA: Die eingebaute Lösung

ZHA ist direkt in Home Assistant integriert. Kein extra Container, kein MQTT-Broker, keine zusätzliche Software. Stick rein, Integration hinzufügen, Geräte pairen.

**Vorteile:**
- **Zero Setup:** Stick einstecken → Integration hinzufügen → fertig
- **Kein MQTT nötig:** Alles läuft intern in Home Assistant
- **Offiziell unterstützt:** Wird vom HA-Team gepflegt
- **SkyConnect Plug-and-Play:** Der offizielle HA-Stick funktioniert sofort mit ZHA

**Nachteile:**
- **Geräte-Support:** Weniger Geräte unterstützt als Z2M, besonders exotische China-Sensoren
- **Updates:** Neue Geräte-Definitionen kommen erst mit HA-Updates (alle 4 Wochen)
- **Weniger Konfiguration:** Manche Geräte-Optionen sind nicht zugänglich
- **Debugging:** Wenn was nicht funktioniert, ist die Fehlersuche schwieriger

## Zigbee2MQTT: Die Power-Lösung

Zigbee2MQTT läuft als separater Dienst (Docker-Container oder HA Add-on) und kommuniziert über MQTT mit Home Assistant. Das klingt komplizierter – ist es auch. Aber dafür bekommst du mehr Kontrolle.

**Vorteile:**
- **Geräte-Support:** Über 4000 Geräte unterstützt. Wenn es Zigbee spricht, läuft es mit Z2M.
- **Schnelle Updates:** Neue Geräte werden oft innerhalb von Tagen hinzugefügt (Community-driven)
- **Eigenes Web-UI:** Dashboard mit Netzwerk-Karte, Geräte-Info, OTA-Updates
- **Unabhängig von HA:** Läuft weiter auch wenn Home Assistant neustartet
- **MQTT:** Andere Systeme können die gleichen Geräte nutzen (Node-RED, Grafana etc.)
- **Geräte-Optionen:** Voller Zugriff auf alle Zigbee-Cluster und Attribute

**Nachteile:**
- **Mehr Setup:** MQTT-Broker + Z2M Container + Konfiguration
- **Extra Ressourcen:** Ein zusätzlicher Container der RAM und CPU braucht
- **Lernkurve:** MQTT-Konzept muss man erstmal verstehen

## Der direkte Vergleich

| Kriterium | ZHA | Zigbee2MQTT |
|-----------|-----|-------------|
| Einrichtung | 2 Minuten | 15-30 Minuten |
| Geräte-Support | ~3000 | ~4000+ |
| Neue Geräte | Alle 4 Wochen (HA-Update) | Innerhalb von Tagen |
| MQTT nötig | Nein | Ja |
| Eigenes Web-UI | Nein | Ja (mit Netzwerk-Karte) |
| OTA-Updates für Geräte | Eingeschränkt | Ja (für viele Geräte) |
| Unabhängig von HA | Nein | Ja |
| Stick-Kompatibilität | Gut (SkyConnect, ConBee, Sonoff) | Sehr gut (Sonoff P optimal) |
| Debugging | Schwierig | Gut (Logs, MQTT Explorer) |
| Ressourcen | Minimal (in HA integriert) | ~100MB RAM extra |

## Wann ZHA die richtige Wahl ist

- Du fängst gerade erst an und willst es einfach haben
- Du nutzt den offiziellen SkyConnect-Stick
- Du hast nur "Standard-Geräte" (Philips Hue, IKEA, Aqara Basics)
- Du willst keinen MQTT-Broker betreiben

## Wann Zigbee2MQTT die richtige Wahl ist

- Du hast exotische Geräte (günstige China-Sensoren, Tuya-Geräte)
- Du willst maximale Kontrolle und Konfigurationsmöglichkeiten
- Du nutzt sowieso schon MQTT (für andere Integrationen)
- Du willst OTA-Firmware-Updates für deine Zigbee-Geräte
- Du nutzt einen [Sonoff ZBDongle-P]({% post_url 2025-02-19-top-5-zigbee-sticks-fuer-home-assistant %}) (die Referenz-Hardware für Z2M)

## Kann man wechseln?

Ja, aber es ist nervig. Beim Wechsel von ZHA zu Z2M (oder umgekehrt) musst du alle Geräte neu pairen. Das heißt: Jedes Gerät in den Pairing-Modus bringen und neu verbinden. Bei 5 Geräten kein Problem, bei 50 ein ganzer Nachmittag.

Deshalb mein Rat: Entscheide dich am Anfang und bleib dabei. Wenn du unsicher bist und einen Sonoff-Stick hast: Nimm Z2M. Der Mehraufwand bei der Einrichtung ist 15 Minuten – dafür hast du langfristig weniger Probleme mit nicht-unterstützten Geräten.

## Mein Setup

Ich nutze Zigbee2MQTT als Docker-Container auf [Unraid]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}), mit Mosquitto als MQTT-Broker. Der [Sonoff ZBDongle-P]({% post_url 2025-02-19-top-5-zigbee-sticks-fuer-home-assistant %}) ist per [USB Passthrough]({% post_url 2025-07-13-install-ha-vm %}) an die Home Assistant VM durchgereicht.

Das Netzwerk läuft seit über einem Jahr ohne einen einzigen Ausfall. Über 40 Geräte, null Probleme. Das war mit dem ConBee II und ZHA damals anders – da gab es regelmäßig Verbindungsabbrüche.

## Fazit

Beide Lösungen funktionieren. ZHA ist einfacher, Z2M ist mächtiger. Für ein Smart Home das wachsen soll und wo man auch mal ein günstiges No-Name-Gerät von AliExpress einbinden will, ist Zigbee2MQTT die bessere Langzeit-Investition.

*Welchen Zigbee-Stick du dafür brauchst, erkläre ich in meinem [Stick-Vergleich]({% post_url 2025-02-19-top-5-zigbee-sticks-fuer-home-assistant %}).*

<style>
  .preview-img { display: none !important; }
</style>
