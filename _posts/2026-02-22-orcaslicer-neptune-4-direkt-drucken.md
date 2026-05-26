---
layout: post
title: "Von Cura zu OrcaSlicer: Direkt vom PC auf den Neptune 4 drucken (ohne SD-Karte)"
date: 2026-02-22
categories: [3D-Druck]
tags: [3D-Druck, Neptune4, Klipper]
description: "OrcaSlicer statt Cura für den Elegoo Neptune 4 – direktes Drucken über WLAN ohne SD-Karte oder manuellen Upload. Einrichtung in 5 Minuten."
image:
  path: /assets/img/posts/orcaslicer-neptune4.jpg
---

Ich habe viel zu lange mit Cura gedruckt. Der Workflow war jedes Mal der gleiche Krampf: Modell slicen, G-Code auf die SD-Karte speichern (oder über das Fluidd-Webinterface hochladen), zum Drucker laufen, Druck starten. Bei jedem einzelnen Druck.

Dann bin ich auf **OrcaSlicer** umgestiegen und hab festgestellt: Das Ding kann den Druck direkt über WLAN an den Neptune 4 schicken und starten. Ein Klick im Slicer – und der Drucker legt los. Keine SD-Karte, kein manueller Upload, kein Aufstehen vom Schreibtisch.

## Warum OrcaSlicer statt Cura?

Cura ist nicht schlecht – aber es ist langsam, aufgebläht und hat keine native Anbindung an Klipper-Drucker. Du musst den G-Code exportieren und dann manuell ins Fluidd/Mainsail-Interface hochladen.

OrcaSlicer (ein Fork von PrusaSlicer/BambuStudio) kann dagegen:
- **Direkt an Klipper senden** – Slice → Print → fertig
- **Schneller slicen** – spürbar flotter als Cura, besonders bei komplexen Modellen
- **Bessere Klipper-Profile** – kennt den Neptune 4 ab Werk, optimierte Settings
- **Druckvorschau** – zeigt dir Layer für Layer wie der Druck aussehen wird
- **Multi-Material** – falls du mal einen AMS oder MMU nachrüstest

## Schritt 1: OrcaSlicer installieren

1. [OrcaSlicer herunterladen](https://github.com/SoftFever/OrcaSlicer/releases){:target="_blank"} (kostenlos, Open Source){:target="_blank"}
2. Installieren und starten
3. Beim ersten Start: **Elegoo → Neptune 4** (oder Neptune 4 Plus/Pro/Max) als Drucker auswählen

OrcaSlicer bringt fertige Profile für den Neptune 4 mit – Düsentemperatur, Bett-Temperatur, Geschwindigkeit, Retraction. Alles voreingestellt und funktioniert sofort.

## Schritt 2: Drucker-Verbindung einrichten

Hier passiert die Magie. OrcaSlicer kann sich direkt mit dem Klipper-Webinterface (Fluidd/Mainsail) deines Neptune 4 verbinden:

1. In OrcaSlicer: **Drucker-Einstellungen** (das Zahnrad neben dem Drucker-Namen)
2. Reiter **"Verbindung"** oder **"Connection"**
3. **Typ:** Klipper (Fluidd/Mainsail/Moonraker)
4. **Host:** Die IP-Adresse deines Neptune 4 (z.B. `192.168.178.55`)
5. **Port:** 7125 (Standard Moonraker-Port)
6. Auf "Test" klicken – wenn "Verbindung erfolgreich" kommt, bist du fertig

Falls der Test fehlschlägt: Prüf ob du das Fluidd-Interface im Browser unter `http://DRUCKER-IP` erreichen kannst. Wenn ja, ist der Port eventuell anders (manche Neptune-Firmware nutzt Port 80 für Moonraker).

## Schritt 3: Drucken mit einem Klick

Ab jetzt ist der Workflow:
1. STL-Datei in OrcaSlicer ziehen
2. Material und Einstellungen wählen (oder Standard lassen)
3. **"Slice"** klicken
4. Statt "Export G-Code" klickst du auf **"Print"** (oder das Drucker-Symbol)
5. OrcaSlicer schickt den G-Code direkt an den Neptune 4
6. Der Drucker heizt auf und startet automatisch

Das war's. Kein SD-Karten-Gefummel, kein Browser öffnen, kein manuelles Hochladen. Du sitzt am Schreibtisch, klickst auf Print, und der Drucker im Nebenzimmer legt los.

## Cura-Profile migrieren? Brauchst du nicht.

Falls du in Cura eigene Profile hattest: Vergiss sie. Die OrcaSlicer-Profile für den Neptune 4 sind ab Werk besser als alles was ich in Cura je zusammengebastelt habe. Die Entwickler haben die Settings speziell für Klipper-Drucker optimiert.

Wenn du trotzdem Werte übernehmen willst (z.B. einen speziellen Retraction-Wert der bei dir gut funktioniert), findest du alle Einstellungen unter dem gleichen Namen – nur an anderer Stelle im Menü.

## Bonus: Druckfortschritt live im Slicer

Während der Druck läuft, zeigt OrcaSlicer den Fortschritt an – Temperatur, Layer, geschätzte Restzeit. In Kombination mit meiner [USB-Kamera am Neptune 4]({% post_url 2026-03-05-neptune-4-usb-kamera %}) habe ich alles im Blick ohne aufzustehen.

## OrcaSlicer vs. Cura: Kurzer Vergleich

| | Cura | OrcaSlicer |
|---|---|---|
| Direkt an Klipper senden | ❌ (Plugin nötig, instabil) | ✅ Nativ |
| Slice-Geschwindigkeit | Langsam | Schnell |
| Neptune 4 Profil | Community-Profil | Offiziell integriert |
| Klipper-Optimierung | Nein | Ja (Pressure Advance, Input Shaper) |
| Preis | Kostenlos | Kostenlos |
| Lernkurve | Mittel | Mittel (ähnliche UI) |

## Fazit

Der Wechsel von Cura zu OrcaSlicer hat meinen 3D-Druck-Workflow komplett verändert. Kein SD-Karten-Tanz mehr, kein Browser-Upload, kein zum-Drucker-Laufen um auf Start zu drücken. Slice → Print → fertig. So sollte es sein.

Die Einrichtung dauert 5 Minuten und du wirst dich fragen, warum du das nicht schon früher gemacht hast.

*Neu beim 3D-Druck? In meinem [Neptune 4 Einsteiger-Guide]({% post_url 2025-11-02-elegoo-neptune-4-einsteiger-guide %}) zeige ich dir den kompletten Weg vom Auspacken bis zum ersten Druck. Und wenn der Drucker läuft, schau dir die [Upgrades an die sich wirklich lohnen]({% post_url 2026-05-18-3d-drucker-upgrades-die-sich-lohnen %}).*

<style>
  .preview-img { display: none !important; }
</style>
