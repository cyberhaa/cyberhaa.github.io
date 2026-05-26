---
layout: post
title: "3D-Drucker Upgrades die sich wirklich lohnen (und welche nicht)"
date: 2026-05-18
categories: [3D-Druck]
tags: [3D-Druck, Neptune4, Klipper]
description: "Welche 3D-Drucker Upgrades bringen wirklich was? PEI-Platte, gehärtete Düsen und Klipper-Tuning – meine ehrliche Erfahrung mit dem Neptune 4."
---

Wenn man einen 3D-Drucker kauft, dauert es ungefähr eine Woche bis man anfängt, Upgrades zu recherchieren. Das Internet ist voll mit "Must-Have Mods" – aber ehrlich gesagt ist die Hälfte davon Geldverschwendung. Zumindest bei modernen Druckern wie dem Elegoo Neptune 4, der ab Werk schon ziemlich gut ist.

Hier sind die Upgrades die bei mir einen echten Unterschied gemacht haben – und ein paar, die ich mir hätte sparen können.

## Lohnt sich: PEI-Federstahlplatte

Das beste Upgrade überhaupt. Die originale Druckplatte vom Neptune 4 funktioniert, aber Teile lösen sich manchmal schwer oder haften nicht gleichmäßig. Eine [PEI-Federstahlplatte *]({{ site.data.amazon.pei_sheet.url }}){:target="_blank"} löst beides:

- **Haftung:** PETG, PLA und ASA haften perfekt ohne Klebestift oder Haarspray
- **Ablösung:** Platte biegen → Teil springt ab. Kein Kratzen mit dem Spachtel mehr
- **Haltbarkeit:** Hält ewig, muss nur ab und zu mit Isopropanol abgewischt werden

Einzige Einschränkung: Bei TPU (flexiblem Filament) haftet es zu gut – da lieber die originale Platte nehmen.

**Kosten:** ~20€. Amortisiert sich nach dem dritten Druck, bei dem du nicht mehr 10 Minuten mit dem Spachtel kämpfst.

## Lohnt sich: Gehärtete Stahl-Düsen

Die Standard-Messing-Düse verschleißt. Nicht sofort, aber nach ein paar Rollen Filament mit Additiven (Carbon-PETG, Glow-in-the-Dark, holzgefüllt) ist sie rund geschliffen und der Druck wird unsauber.

Ein Set [gehärtete Stahl-Düsen *]({{ site.data.amazon.hardened_nozzle.url }}){:target="_blank"} kostet 10€ und hält quasi ewig. Der einzige Nachteil: Stahl leitet Wärme etwas schlechter als Messing – bei PLA merkst du das nicht, bei schnellem PETG-Druck musst du eventuell 5°C höher gehen.

**Mein Tipp:** Messing-Düse für normales PLA/PETG, gehärtete Düse für alles mit Partikeln drin.

## Lohnt sich: Capricorn PTFE-Schlauch

Der originale weiße PTFE-Schlauch (Bowden-Tube) hat einen etwas größeren Innendurchmesser als nötig. Das führt zu Stringing und ungenauen Retraction-Werten. Der [Capricorn-Schlauch *]({{ site.data.amazon.capricorn_tube.url }}){:target="_blank"} hat engere Toleranzen und ist hitzebeständiger.

**Ergebnis:** Weniger Stringing bei PETG, sauberere Retractions, weniger Verstopfungen. Für 8€ ein No-Brainer.

## Lohnt sich: Klipper-Tuning (kostenlos!)

Der Neptune 4 läuft ab Werk mit Klipper – aber die Standard-Konfiguration ist konservativ. Mit ein paar Anpassungen in der `printer.cfg` holst du deutlich mehr raus:

- **Input Shaper kalibrieren:** Reduziert Ghosting/Ringing bei hohen Geschwindigkeiten
- **Pressure Advance tunen:** Sauberere Ecken und weniger Blobs
- **Acceleration erhöhen:** Von 3000 auf 5000-8000 mm/s² (nach Input Shaper Kalibrierung)

Das kostet nichts außer einer Stunde Einarbeitung. Die Druckqualität bei hoher Geschwindigkeit verbessert sich dramatisch.

## Lohnt sich NICHT: All-Metal Hotend (beim Neptune 4)

Viele empfehlen ein All-Metal Hotend für höhere Temperaturen. Beim Neptune 4 ist das ab Werk schon verbaut. Spar dir das Geld.

## Lohnt sich NICHT: Linearschienen-Umbau

Ja, Linearschienen sind präziser als die V-Slot-Rollen. Aber der Unterschied ist bei einem gut eingestellten Neptune 4 minimal. Der Umbau kostet 50-80€, dauert einen halben Tag und bringt vielleicht 5% bessere Druckqualität. Das Geld ist in Filament besser investiert.

## Lohnt sich NICHT: Enclosure (für PLA/PETG)

Ein Gehäuse um den Drucker ist nur nötig wenn du ASA oder ABS druckst (wegen Warping und Dämpfen). Für PLA und PETG ist es sogar kontraproduktiv – die brauchen Kühlung, kein warmes Gehäuse.

## Meine Upgrade-Reihenfolge

Wenn du gerade erst anfängst und nicht alles auf einmal kaufen willst:

1. **PEI-Platte** (~20€) – sofort spürbarer Unterschied
2. **Capricorn-Schlauch** (~8€) – schnell getauscht, weniger Stringing
3. **Klipper-Tuning** (kostenlos) – Input Shaper + Pressure Advance
4. **Gehärtete Düsen** (~10€) – sobald du abrasive Filamente drucken willst
5. **[USB-Kamera]({% post_url 2026-03-05-neptune-4-usb-kamera %})** (~15€) – Drucke remote überwachen

Gesamtkosten für alle sinnvollen Upgrades: unter 60€. Damit hast du einen Drucker, der mit Geräten für das Doppelte mithalten kann.

## Fazit

Weniger ist mehr. Die meisten "Must-Have" Upgrades aus YouTube-Videos sind für Klicks optimiert, nicht für echten Mehrwert. Ein Neptune 4 mit PEI-Platte, Capricorn-Schlauch und gut kalibriertem Klipper druckt besser als ein teurer Drucker mit Standard-Settings.

Spar dir das Geld für Upgrades die keinen Unterschied machen – und investier es lieber in gutes [Filament für deine Outdoor-Projekte]({% post_url 2026-02-01-petg-pla-asa-filament-vergleich-outdoor %}).

<style>
  .preview-img { display: none !important; }
</style>
