---
layout: post
title: "Überwachungskamera lokal mit Home Assistant: AgentDVR statt Cloud"
date: 2025-09-14
categories: [Smart Home]
tags: [HomeAssistant, Unraid, Sicherheit]
description: "Überwachungskameras ohne Cloud betreiben – mit AgentDVR auf Unraid und Home Assistant. Lokale Aufzeichnung, Bewegungserkennung und Push-Benachrichtigung."
image:
  path: /assets/img/posts/agentdvr-kamera.jpg
---

Die meisten Überwachungskameras wollen dich in ein Cloud-Abo drücken. Ring, Arlo, Nest – alle speichern deine Videos auf fremden Servern und verlangen monatlich Geld dafür. Und wenn der Hersteller den Dienst einstellt? Dann hast du eine teure Kamera ohne Aufzeichnung.

Bei mir läuft das anders: Meine Kameras streamen direkt in **AgentDVR** auf meinem [Unraid-Server]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}). Lokale Aufzeichnung, Bewegungserkennung, Push-Benachrichtigungen über Home Assistant – alles ohne Cloud, alles unter meiner Kontrolle.

## Warum AgentDVR?

Es gibt mehrere NVR-Lösungen (Network Video Recorder) für den Heimgebrauch: Frigate, Blue Iris, Shinobi, AgentDVR. Ich habe mich für AgentDVR entschieden weil:

- **Kostenlos** für den privaten Einsatz (unbegrenzt Kameras)
- **Web-UI** – Konfiguration komplett im Browser, kein Kommandozeilen-Gefummel
- **Docker-Container** – läuft sauber auf Unraid
- **ONVIF-Support** – funktioniert mit quasi jeder IP-Kamera
- **Bewegungserkennung** eingebaut (Zonen definierbar)
- **Zeitplan** – Aufzeichnung nur nachts oder wenn niemand da ist
- **RTSP-Restream** – stellt die Kamera-Streams für Home Assistant bereit

## Hardware: Welche Kameras?

Für ein lokales Setup brauchst du Kameras die **RTSP** oder **ONVIF** unterstützen. Das sind die offenen Protokolle, mit denen du den Video-Stream direkt abgreifen kannst – ohne App, ohne Cloud.

Meine Empfehlungen:
- **Outdoor (4K):** [Reolink RLC-811A *]({{ site.data.amazon.reolink_811a.url }}){:target="_blank"} – PoE, 4K, Personen-/Fahrzeugerkennung on-board, kein Abo
- **Outdoor (Budget):** [Reolink RLC-510A *]({{ site.data.amazon.reolink_510a.url }}){:target="_blank"} – PoE, 5MP, solide Nachtsicht

**Warum Reolink?** Die Kameras haben RTSP ab Werk aktiviert, brauchen keine Cloud-Registrierung und funktionieren komplett offline. Außerdem PoE – ein Kabel für Strom und Daten, kein extra Netzteil nötig.

**Für PoE brauchst du:** Einen PoE-Switch wie den [UniFi Switch Lite 8 PoE *]({{ site.data.amazon.poe_switch_unifi.url }}){:target="_blank"} oder einen günstigen TP-Link PoE-Switch. Der versorgt die Kameras über das Netzwerkkabel mit Strom.

## AgentDVR auf Unraid installieren

1. In Unraid **Community Applications** nach "AgentDVR" suchen
2. Container installieren – Standard-Einstellungen sind okay
3. Speicherpfad für Aufnahmen festlegen (z.B. `/mnt/user/surveillance/`)
4. Port merken (Standard: 8090)

Nach dem Start erreichst du AgentDVR unter `http://DEINE-UNRAID-IP:8090`.

## Kameras hinzufügen

1. Im AgentDVR Web-UI auf **"Add Device"** → **"IP Camera"**
2. **RTSP-URL** deiner Kamera eingeben. Bei Reolink ist das meistens:
   ```
   rtsp://admin:DEIN_PASSWORT@KAMERA-IP:554/h264Preview_01_main
   ```
3. Verbindung testen → Live-Bild sollte erscheinen
4. **Bewegungserkennung** aktivieren und Zonen einzeichnen (z.B. nur die Einfahrt, nicht die Straße)

## Aufzeichnung konfigurieren

Ich nehme nicht 24/7 auf – das frisst zu viel Speicher. Stattdessen:
- **Bewegungserkennung:** Aufnahme startet nur wenn sich was bewegt
- **Zeitplan:** Nachts (22:00 - 06:00) immer aufnehmen, tagsüber nur bei Bewegung
- **Speicherlimit:** Alte Aufnahmen nach 14 Tagen automatisch löschen

Mit zwei 5MP-Kameras und Bewegungserkennung brauche ich etwa 50-100GB pro Monat. Das ist auf einer [4TB NAS-Festplatte *]({{ site.data.amazon.nas_hdd_4tb.url }}){:target="_blank"} kein Problem.

## Home Assistant Integration

AgentDVR stellt die Kamera-Streams per RTSP bereit. In Home Assistant fügst du sie als **Generic Camera** hinzu:

1. **Einstellungen → Geräte & Dienste → Integration hinzufügen → Generic Camera**
2. Stream-URL: `rtsp://UNRAID-IP:8090/live/KAMERA-ID`
3. Fertig – die Kamera erscheint als Entity in HA

Jetzt kannst du:
- Das Live-Bild auf deinem Dashboard anzeigen
- Bei Bewegung eine Push-Nachricht aufs Handy schicken
- Einen Snapshot machen und per Telegram/Signal senden
- Die Kamera in Automatisierungen einbinden

## Automatisierung: Benachrichtigung bei Bewegung

```yaml
automation:
  - alias: "Kamera: Bewegung an der Haustür"
    trigger:
      - platform: state
        entity_id: binary_sensor.agentdvr_einfahrt_motion
        to: "on"
    condition:
      - condition: state
        entity_id: group.all_persons
        state: "not_home"
    action:
      - service: camera.snapshot
        target:
          entity_id: camera.einfahrt
        data:
          filename: "/config/www/snapshot_einfahrt.jpg"
      - service: notify.mobile_app
        data:
          title: "Bewegung erkannt!"
          message: "Jemand ist an der Einfahrt."
          data:
            image: "/local/snapshot_einfahrt.jpg"
```

Nur wenn niemand zu Hause ist, bekomme ich eine Benachrichtigung mit Snapshot. Wenn die Familie da ist, will ich nicht bei jeder Katze im Garten gestört werden.

## Warum nicht Frigate?

Frigate ist die andere populäre Lösung und hat bessere KI-Erkennung (Personen, Autos, Tiere). Aber: Frigate braucht idealerweise einen Google Coral TPU für die Objekterkennung, die Konfiguration läuft komplett über YAML-Dateien, und es ist deutlich komplexer einzurichten.

AgentDVR ist für mich der bessere Kompromiss: Web-UI für die Konfiguration, funktioniert ohne extra Hardware, und die eingebaute Bewegungserkennung reicht für den Hausgebrauch völlig aus.

## Fazit

Für unter 100€ pro Kamera (Reolink + PoE-Kabel) hast du ein lokales Überwachungssystem ohne monatliche Kosten, ohne Cloud und ohne die Angst, dass der Hersteller den Dienst abschaltet. AgentDVR auf Unraid ist in 10 Minuten eingerichtet und die Integration in Home Assistant gibt dir volle Kontrolle über Benachrichtigungen und Automatisierungen.

*Für das Netzwerk dahinter (PoE-Switch, VLANs für Kamera-Isolation) schau dir meinen [UniFi-Artikel]({% post_url 2026-05-24-unifi-netzwerk-smart-home %}) an.*

<style>
  .preview-img { display: none !important; }
</style>
