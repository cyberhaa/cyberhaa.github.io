---
layout: post
title: "PETG vs. PLA vs. ASA: Welches Filament für Outdoor-Projekte?"
date: 2026-02-01
categories: [3D-Druck]
tags: [3D-Druck, Neptune4]
description: "PETG, PLA oder ASA für den Außeneinsatz? Ehrlicher Vergleich mit Praxiserfahrung – Landroid-Räder, Gartenhalterungen und Outdoor-Gehäuse."
image:
  path: /assets/img/posts/3d-druck-filamente.jpg
---

Wer seinen 3D-Drucker nicht nur für Deko-Figuren nutzt, steht irgendwann vor der Frage: Welches Filament hält draußen? Meine [Landroid Traktor-Räder]({% post_url 2026-04-12-worx-landroid-tuning-3d-druck-home-assistant %}) stehen das ganze Jahr im Garten – Sonne, Regen, Frost. Da kann man nicht einfach PLA nehmen und hoffen.

![Verschiedene 3D-Druck Filamente (PLA, PETG, ASA) im Vergleich](/assets/img/posts/3d-druck-filamente.jpg){: .shadow .rounded-10 }
_Die Qual der Wahl: Welches Filament für welchen Zweck?_

Hier ist mein ehrlicher Vergleich nach über einem Jahr Praxiserfahrung mit allen drei Materialien im Außeneinsatz.

## PLA: Der Einsteiger-Liebling

[PLA *]({{ site.data.amazon.pla_filament.url }}){:target="_blank"} ist das Material, mit dem jeder anfängt. Druckt sich butterweich, riecht kaum, braucht kein beheiztes Bett und verzieht sich nicht.

**Draußen taugt es trotzdem nichts:**
- Ab ~55°C wird es weich – ein schwarzes Teil in der Sonne erreicht das locker
- UV-Strahlung macht es spröde – nach ein paar Monaten bricht es bei Belastung
- Feuchtigkeit zieht ein und schwächt die Schichtverbindung

**Wann PLA okay ist:** Teile die im Schatten hängen und keine mechanische Belastung haben. Briefkasten-Schilder, Kabelclips unter dem Dachüberstand. Sonst: Finger weg für Outdoor.

## PETG: Mein Standard für draußen

[PETG *]({{ site.data.amazon.petg_filament_schwarz.url }}){:target="_blank"} ist mein Go-to für alles, was nach draußen muss. Es ist der beste Kompromiss aus Druckbarkeit und Haltbarkeit.

**Warum PETG für Outdoor funktioniert:**
- Hitzebeständig bis ~80°C – überlebt jeden deutschen Sommer
- UV-beständiger als PLA (nicht perfekt, aber deutlich besser)
- Flexibler als PLA – bricht nicht so schnell bei Schlägen
- Chemisch resistent gegen die meisten Reinigungsmittel

**Meine Erfahrung:** Die Landroid-Räder aus schwarzem PETG laufen seit über einem Jahr im Garten. Kein Verziehen, kein Bruch, keine sichtbare Degradation. Auch meine Gartenhalterungen für Sensoren und Kabel sind alle aus PETG.

**Nachteile:**
- Stringing beim Drucken (die nervigen Fäden) – mit guten Retraction-Settings beherrschbar
- Nicht so knackig-hart wie PLA – Gewinde und Snap-Fits sind etwas weicher
- Braucht beheiztes Bett (~70-80°C)

**Druckeinstellungen für meinen Neptune 4:**
- Düse: 230-240°C
- Bett: 75°C
- Speed: 60-80mm/s (langsamer als PLA)
- Lüfter: 30-50% (nicht voll aufdrehen)

## ASA: Der Profi für extreme Bedingungen

[ASA *]({{ site.data.amazon.asa_filament.url }}){:target="_blank"} ist quasi ABS mit UV-Schutz. Es ist das Material, aus dem Autoteile und Gartenmöbel im Spritzguss gefertigt werden.

**Warum ASA der König für Outdoor ist:**
- Beste UV-Beständigkeit aller gängigen Filamente
- Hitzebeständig bis ~100°C
- Extrem schlagfest und zäh
- Behält seine Farbe über Jahre

**Warum ich es trotzdem selten nutze:**
- Stinkt beim Drucken (Styrol-Dämpfe) – Drucker muss in einem belüfteten Raum stehen oder ein Gehäuse mit Abluft haben
- Verzieht sich stark (Warping) – braucht ein Gehäuse (Enclosure) um den Drucker
- Haftet schlecht auf dem Bett – Klebestift oder spezielles Spray nötig
- Teurer als PETG

**Wann ASA sich lohnt:** Teile die jahrelang in der prallen Sonne stehen und mechanisch belastet werden. Halterungen für Überwachungskameras, Wetterstationen, Outdoor-Gehäuse für Elektronik.

## Meine Empfehlung

| Einsatz | Material | Warum |
|---------|----------|-------|
| Indoor (Deko, Halterungen) | PLA | Einfach zu drucken, sieht gut aus |
| Outdoor (Garten, Balkon) | PETG | Bester Kompromiss, hält Jahre |
| Outdoor extrem (pralle Sonne, mechanisch) | ASA | Wenn PETG nicht reicht |
| Landroid-Räder, Sensor-Gehäuse | PETG | Bewährt, günstig, haltbar |

Für 90% aller Outdoor-Projekte ist **PETG die richtige Wahl**. ASA lohnt sich nur, wenn du weißt dass das Teil jahrelang in der prallen Mittagssonne hängt und dabei Belastung aushalten muss. Und PLA hat draußen einfach nichts verloren – egal was manche YouTube-Videos behaupten.

*Wenn du wissen willst, was man mit PETG alles drucken kann: Meine [Landroid Traktor-Räder]({% post_url 2026-04-12-worx-landroid-tuning-3d-druck-home-assistant %}) laufen seit über einem Jahr problemlos im Garten.*

<style>
  /* Versteckt das automatische Header-Bild ganz oben im Artikel */
  .preview-img { display: none !important; }
</style>
