---
layout: post
title: "Home Assistant Automatisierungen für Einsteiger: 5 Ideen zum Sofort-Nachmachen"
date: 2026-02-15
categories: [Smart Home]
tags: [HomeAssistant, Zigbee]
description: "5 praktische Home Assistant Automatisierungen für Einsteiger – Licht bei Bewegung, Heizung nach Zeitplan, Fenster-Warnung und mehr."
image:
  path: /assets/img/posts/ha-automatisierungen.jpg
---

Home Assistant ist installiert, die ersten Geräte sind eingebunden – und jetzt? Viele bleiben beim manuellen Schalten über die App hängen. Dabei sind Automatisierungen der eigentliche Grund, warum sich ein Smart Home lohnt: Das Haus denkt mit, ohne dass du ständig zum Handy greifen musst.

Hier sind 5 Automatisierungen, die bei mir seit Tag 1 laufen und die du in 5 Minuten nachbauen kannst. Alles über den visuellen Editor – kein YAML nötig.

## 1. Licht an bei Bewegung (und aus nach 3 Minuten)

Die Klassiker-Automatisierung. Ein [Bewegungssensor *]({{ site.data.amazon.aqara_motion.url }}){:target="_blank"} im Flur, und das Licht geht automatisch an wenn jemand vorbeiläuft. Nach 3 Minuten ohne Bewegung wieder aus.

**Was du brauchst:**
- Einen Zigbee-Bewegungssensor (z.B. Aqara P1)
- Eine smarte Lampe oder einen Shelly hinter dem Lichtschalter

**So baust du es:**
1. Einstellungen → Automatisierungen → Neue Automatisierung
2. **Auslöser:** Zustand → Bewegungssensor → "Bewegung erkannt"
3. **Aktion:** Dienst aufrufen → `light.turn_on` → deine Lampe
4. **Zweite Automatisierung** für "Licht aus": Auslöser = Bewegungssensor → "Keine Bewegung" für 3 Minuten → `light.turn_off`

**Tipp:** Bau eine Bedingung ein, die das Licht nur abends einschaltet (z.B. nach Sonnenuntergang). Tagsüber braucht man im Flur kein Licht.

## 2. Heizung runter wenn das Fenster offen ist

Jeder kennt es: Man lüftet und vergisst, die Heizung runterzudrehen. Mit einem [Tür-/Fenstersensor *]({{ site.data.amazon.aqara_door.url }}){:target="_blank"} für 10€ ist das Problem gelöst.

**So baust du es:**
1. **Auslöser:** Fenstersensor → "offen" für 2 Minuten
2. **Aktion:** Thermostat auf 5°C setzen (Frostschutz)
3. **Zweite Automatisierung:** Fenstersensor → "geschlossen" → Thermostat zurück auf Wunschtemperatur

Die 2 Minuten Verzögerung sind wichtig – sonst springt die Heizung bei jedem kurzen Fenster-Öffnen hin und her.

## 3. Benachrichtigung wenn die Waschmaschine fertig ist

Kein smartes Gerät nötig – nur eine [smarte Steckdose mit Strommessung *]({{ site.data.amazon.shelly_plug_s.url }}){:target="_blank"}. Die Waschmaschine zieht während des Waschgangs 200-2000W. Wenn der Verbrauch unter 5W fällt, ist sie fertig.

**So baust du es:**
1. **Auslöser:** Stromsensor der Steckdose → unter 5W für 3 Minuten
2. **Bedingung:** Stromsensor war in den letzten 30 Minuten über 50W (damit die Automatisierung nicht auslöst wenn die Maschine einfach aus ist)
3. **Aktion:** Push-Nachricht aufs Handy: "Waschmaschine ist fertig!"

Funktioniert genauso für Trockner und Spülmaschine.

## 4. Alles aus wenn niemand zu Hause ist

Home Assistant weiß über die UniFi-Integration oder die Handy-App, wer zu Hause ist. Wenn die letzte Person das Haus verlässt:

**So baust du es:**
1. **Auslöser:** Zone "Zuhause" → Anzahl Personen = 0
2. **Aktion:** Szene "Alles aus" aktivieren (alle Lichter aus, Heizung auf Eco, Steckdosen aus)

Und wenn die erste Person wieder nach Hause kommt:
1. **Auslöser:** Zone "Zuhause" → Anzahl Personen von 0 auf 1
2. **Aktion:** Flur-Licht an, Heizung auf Komfort

## 5. Guten-Morgen-Routine

Jeden Morgen um 6:30 (nur an Werktagen):
- Flur-Licht auf 20% (nicht blenden)
- Kaffeemaschine einschalten (über smarte Steckdose)
- Rollladen hochfahren (falls vorhanden)

**So baust du es:**
1. **Auslöser:** Zeit → 06:30
2. **Bedingung:** Wochentag ist Mo-Fr
3. **Aktionen:** Licht dimmen, Steckdose einschalten, Rollladen hoch

Am Wochenende passiert nichts – da darf man ausschlafen.

## Bonus-Tipp: Der Helfer "input_boolean"

Manchmal willst du eine Automatisierung temporär abschalten können, ohne sie zu löschen. Dafür gibt es "Helfer" in Home Assistant:

1. Einstellungen → Geräte & Dienste → Helfer → Schalter erstellen ("Automatisierungen aktiv")
2. In deiner Automatisierung eine Bedingung hinzufügen: Nur ausführen wenn der Helfer "an" ist
3. Den Helfer als Kachel aufs Dashboard legen

So kannst du z.B. die Bewegungsmelder-Automatisierung abschalten wenn Besuch da ist, ohne in die Einstellungen zu müssen.

## Fazit

Das Schöne an diesen Automatisierungen: Sie sind simpel, aber man merkt den Unterschied sofort im Alltag. Das Haus fühlt sich "intelligent" an, ohne dass man ständig am Handy hängt. Und das Beste: Alles läuft lokal auf deinem [Home Assistant Server]({% post_url 2025-07-13-install-ha-vm %}) – keine Cloud, keine Verzögerung.

*Für die Hardware-Grundlage (Zigbee-Sensoren, Sticks) schau dir meinen [Zigbee-Stick Vergleich]({% post_url 2025-02-19-top-5-zigbee-sticks-fuer-home-assistant %}) an.*

<style>
  .preview-img { display: none !important; }
</style>
