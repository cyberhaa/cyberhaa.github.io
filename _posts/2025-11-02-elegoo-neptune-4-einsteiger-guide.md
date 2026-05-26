---
layout: post
title: "Elegoo Neptune 4 Einsteiger-Guide: Vom Auspacken zum ersten Druck in 30 Minuten"
date: 2025-11-02
categories: [3D-Druck]
tags: [3D-Druck, Neptune4, Klipper]
description: "Elegoo Neptune 4 einrichten – vom Auspacken über die Kalibrierung bis zum ersten erfolgreichen Druck. Schritt für Schritt für Einsteiger."
---

Du hast dir einen [Elegoo Neptune 4 *]({{ site.data.amazon.neptune_4_plus.url }}){:target="_blank"} bestellt und der Karton steht vor dir. Und jetzt? YouTube-Videos zum Thema sind entweder 45 Minuten lang oder setzen voraus, dass du schon weißt was "Z-Offset" und "Flow Rate" bedeuten.

Hier ist die Kurzversion: Vom Auspacken bis zum ersten fertigen Druck in 30 Minuten. Ohne Vorwissen, ohne Frust.

## Schritt 1: Zusammenbauen (10 Minuten)

Der Neptune 4 kommt zu 90% vormontiert. Du musst nur:
1. **Oberen Rahmen** (mit dem Druckkopf) auf den unteren schrauben – 4 Schrauben
2. **Bildschirm** anschrauben – 2 Schrauben
3. **Kabel verbinden** – 3 Stecker, alle unterschiedlich groß (kann man nicht falsch machen)
4. **Filament-Halter** oben draufstecken

Werkzeug liegt bei. Das Ganze dauert wirklich nur 10 Minuten wenn man nicht jeden Schritt dreimal in der Anleitung nachliest.

## Schritt 2: Erster Start und Auto-Leveling

Strom rein, einschalten. Der Neptune 4 hat Klipper vorinstalliert – das ist die schnelle Firmware die auch teure Drucker nutzen. Du musst dich damit aber erstmal nicht beschäftigen.

**Auto-Leveling starten:**
1. Am Touchscreen: **Vorbereiten → Auto-Leveling**
2. Der Drucker fährt 25-36 Punkte auf dem Bett ab und misst den Abstand
3. Dauert 2-3 Minuten, danach ist das Bett kalibriert

Das musst du nur einmal machen (und nach jedem Umstellen des Druckers wiederholen).

## Schritt 3: Z-Offset einstellen

Das ist der einzige Schritt der etwas Fingerspitzengefühl braucht. Der Z-Offset bestimmt, wie nah die Düse am Bett ist beim ersten Layer.

1. **Vorbereiten → Z-Offset**
2. Der Druckkopf fährt in die Mitte
3. Schieb ein Blatt Papier zwischen Düse und Bett
4. Dreh am Rad (am Bildschirm) bis das Papier gerade noch so durchziehbar ist – mit leichtem Widerstand
5. Speichern

**Zu weit weg:** Filament haftet nicht, erster Layer löst sich
**Zu nah:** Düse kratzt übers Bett, Filament wird plattgedrückt

Im Zweifel lieber etwas zu nah als zu weit weg. Du kannst den Wert jederzeit in 0.05mm-Schritten anpassen.

## Schritt 4: Filament einlegen

1. [PLA Filament *]({{ site.data.amazon.pla_filament.url }}){:target="_blank"} auf den Halter stecken
2. Ende des Filaments schräg abschneiden (damit es besser in den Einzug gleitet)
3. Am Touchscreen: **Vorbereiten → Filament → Laden**
4. Filament in den Einzug schieben bis der Motor es greift
5. Warten bis sauberes Filament aus der Düse kommt

Wenn es nicht sofort kommt: Düse muss erst aufheizen (190-200°C für PLA). Der Drucker macht das automatisch.

## Schritt 5: Erste Datei drucken

Auf der mitgelieferten SD-Karte sind Test-Dateien. Steck sie ein und wähle am Bildschirm eine aus. Ich empfehle den **Benchy** (das kleine Boot) – das ist der Standard-Testdruck der Welt.

1. SD-Karte einstecken
2. **Drucken → SD-Karte → benchy.gcode** auswählen
3. Start drücken
4. Zuschauen wie der Drucker loslegt

Der Benchy dauert je nach Einstellung 15-25 Minuten auf dem Neptune 4 (der ist schnell dank Klipper). Wenn der erste Layer sauber haftet und nicht abblättert, ist alles richtig eingestellt.

## Schritt 6: Eigene Modelle drucken (Slicer einrichten)

Für eigene Modelle brauchst du einen **Slicer** – das ist die Software die 3D-Modelle (STL-Dateien) in Druckanweisungen (G-Code) umwandelt.

**Meine Empfehlung: OrcaSlicer** (kostenlos, Open Source)
1. [OrcaSlicer herunterladen](https://github.com/SoftFever/OrcaSlicer/releases){:target="_blank"}
2. Beim ersten Start: Drucker auswählen → Elegoo Neptune 4 (ist voreingestellt)
3. STL-Datei reinziehen → "Slice" klicken → auf SD-Karte speichern → drucken

Die Standard-Profile von OrcaSlicer für den Neptune 4 funktionieren sofort gut. Feintuning kommt später.

## Häufige Probleme am Anfang

**Erster Layer haftet nicht:**
- Z-Offset zu hoch → näher ans Bett (0.05mm Schritte)
- Bett nicht sauber → mit Isopropanol abwischen
- Bett-Temperatur zu niedrig → 60°C für PLA

**Filament kommt nicht aus der Düse:**
- Düse nicht heiß genug → mindestens 200°C für PLA
- Verstopfung → "Filament entladen" und neu laden
- Bowden-Schlauch nicht richtig eingesteckt

**Druck löst sich nach ein paar Layern:**
- Bett-Haftung verbessern: Brim aktivieren im Slicer
- Zugluft vermeiden (Fenster zu, keine Klimaanlage auf den Drucker richten)

## Was kommt danach?

Wenn der erste Benchy sauber aussieht, bist du bereit für echte Projekte:
- [USB-Kamera montieren]({% post_url 2026-03-05-neptune-4-usb-kamera %}) um Drucke remote zu überwachen
- [PETG für Outdoor-Projekte]({% post_url 2026-02-01-petg-pla-asa-filament-vergleich-outdoor %}) ausprobieren
- [Upgrades die sich lohnen]({% post_url 2026-05-18-3d-drucker-upgrades-die-sich-lohnen %}) (PEI-Platte, Capricorn-Schlauch)
- [Landroid-Räder drucken]({% post_url 2026-04-12-worx-landroid-tuning-3d-druck-home-assistant %}) als erstes "echtes" Projekt
- [Kaffeemaschinen-Teile reparieren]({% post_url 2026-05-03-siemens-eq6-3d-druck-ersatzteile %}) statt neu kaufen

## Fazit

Der Neptune 4 ist ein verdammt guter Einsteiger-Drucker. Klipper ab Werk, schnell, und die Druckqualität stimmt ohne stundenlange Kalibrierung. Die 30 Minuten vom Auspacken bis zum ersten Druck sind realistisch – wenn man nicht anfängt an Einstellungen rumzudrehen die man noch nicht versteht.

Mein Rat: Druck erstmal 5-10 Sachen mit den Standard-Settings. Lern den Drucker kennen. Und dann fang an zu optimieren.

<style>
  .preview-img { display: none !important; }
</style>
