---
layout: post
title: "Worx Landroid Tuning: 3D-Druck Mods & Home Assistant Automatisierung"
date: 2026-04-12
categories: [3D-Druck]
tags: [Mähroboter, 3D-Druck, HomeAssistant]
description: "Worx Landroid WR148E mit 3D-gedruckten Traktor-Rädern und 6-Klingen-Scheibe tunen, plus smarte Mäh-Automatisierung über Home Assistant."
image:
  path: /assets/img/posts/worx.jpeg
---

Mein [Worx Landroid M700 (WR148E) *]({{ site.data.amazon.landroid_m700.url }}) mäht seit einer Weile zuverlässig den Garten. Aber "zuverlässig" heißt bei Mährobotern ungefähr das Gleiche wie "funktioniert meistens". Die Original-Räder drehen bei feuchtem Gras durch wie ein Hamster im Laufrad, die einzelne Klinge hinterlässt bei schnellem Wachstum eher Streifen als einen sauberen Schnitt, und der Mäher fährt auch fröhlich los, wenn es gerade schüttet oder die Kinder im Garten Fußball spielen.

Ich habe drei Mods gemacht, die das Ding spürbar besser gemacht haben – zwei davon kommen aus dem 3D-Drucker, der dritte aus Home Assistant.

![Worx Landroid WR148E mit 3D-gedruckten Traktor-Rädern auf dem Rasen](/assets/img/posts/worx.jpeg){: .shadow .rounded-10 }
_Mein Landroid WR148E mit den neuen Traktor-Rädern_

## Mod 1: Traktor-Räder aus dem 3D-Drucker

Das größte Ärgernis mit dem Landroid auf unebenem oder leicht feuchtem Rasen: Die glatten Original-Räder drehen durch. Der Mäher bleibt hängen, dreht sich im Kreis oder gibt bei kleinen Steigungen einfach auf und fährt zurück zur Station. Nervig.

Die Lösung sind 3D-gedruckte Traktor-Räder mit aggressivem Profil. Ich nutze das Modell von [Printables (Modell #441847)](https://www.printables.com/model/441847), das speziell für den WR148E designed wurde.

### Was du brauchst
- 3D-Drucker (ich nutze meinen [Elegoo Neptune 4]({% post_url 2026-03-05-neptune-4-usb-kamera %}))
- [PETG Filament *]({{ site.data.amazon.petg_filament.url }}) – PLA ist zu spröde für den Außeneinsatz!
- Die STL-Dateien vom Printables-Link oben

### Druckeinstellungen
- **Material:** PETG (UV- und wetterbeständig)
- **Layer:** 0.2mm
- **Infill:** 40-60%
- **Supports:** Nicht nötig (V02 Version)

Pro Rad brauchst du drei Teile: Reifen, Felgenabdeckung und Nabe. Das Ganze **2x drucken** (links + rechts). Ganz wichtig: Die Originalgewichte aus den alten Rädern müssen in die neuen Naben eingesetzt werden, sonst erkennt der Landroid die Räder nicht korrekt.

### Vorderräder nicht vergessen!
Die neuen Hinterräder haben einen größeren Durchmesser (220mm statt original). Damit der Mäher nicht schief steht, brauchst du auch Verstärkungen für die Vorderräder. Dafür gibt es Slide-On Aufsätze auf [Printables (Modell #529424)](https://www.printables.com/model/529424), die einfach auf die bestehenden Vorderräder aufgeschoben werden. **2x drucken** (der WR148E hat zwei Vorderräder) mit 80-100% Infill.

**Das Ergebnis:** Der Landroid krallt sich jetzt förmlich in den Boden. Steigungen, die vorher zum Abbruch geführt haben, nimmt er einfach so. Bei nassem Gras dreht auch nichts mehr durch. Hätte ich mal gleich gemacht.

## Mod 2: 6-Klingen-Messerscheibe

Der Landroid kommt ab Werk mit einer Scheibe, an der drei kleine Klingen hängen. Das funktioniert so lala, aber gerade im Frühling, wenn das Gras explodiert, sieht man deutlich die Schnittspuren. Die Lösung ist simpel: mehr Klingen.

Auf Printables gibt es eine kostenlose 6-Klingen-Scheibe: [Modell #203620 von Crazy3D](https://www.printables.com/model/203620). Die kommt sogar mit einem Adapter, damit sie auf die Original-Aufnahme passt.

### Was du brauchst
- Die STL-Dateien: `Cutting_disc_6_blades.stl` + `Adapter_original_height.stl`
- 6x [Worx Ersatzklingen WA0190 *]({{ site.data.amazon.landroid_klingen.url }}) (die Standard-Klingen passen weiterhin!)
- 6x [M4 Gewindeeinsätze *]({{ site.data.amazon.gewindeeinsaetze_m4.url }}) (mit dem Lötkolben einschmelzen)

### Druckeinstellungen
- **Material:** PETG
- **Layer:** 0.2mm
- **Infill:** 100% – die Scheibe muss die Vibrationen und Fliehkräfte aushalten!

Die Gewindeeinsätze werden mit einem Lötkolben in die vorgesehenen Löcher eingeschmolzen. Klingen drauf, festschrauben, Scheibe auf den Landroid – fertig.

**Das Ergebnis:** Der Rasen sieht deutlich gleichmäßiger aus. Durch die doppelte Klingenanzahl wird jeder Grashalm öfter erwischt, auch wenn der Mäher etwas schneller fährt.

## Mod 3: Smarte Mäh-Automatisierung mit Home Assistant

Der Landroid hat zwar eine eigene App mit Zeitplan, aber die ist ziemlich dumm. Sie weiß nicht, ob es regnet, ob jemand im Garten ist oder ob der Akku überhaupt voll genug ist. Der Mäher fährt einfach stur los, wenn die Uhr klingelt.

Über die HACS-Integration **landroid_cloud** lässt sich der Mäher komplett in Home Assistant einbinden. Damit habe ich mir eine Automatisierung gebaut, die den Landroid nur dann losschickt, wenn die Bedingungen stimmen.

### Voraussetzung
1. Home Assistant läuft (z.B. als [VM auf Unraid]({% post_url 2025-07-13-install-ha-vm %}))
2. HACS installiert
3. In HACS nach "Landroid Cloud" suchen und installieren
4. Mit deinem Worx-Konto anmelden – der Mäher taucht als `lawn_mower`-Entity auf

### Meine Automatisierung: Smart Mähen

```yaml
automation:
  - alias: "Landroid Smart Mähen"
    trigger:
      - platform: time
        at: "08:00:00"
    condition:
      - condition: state
        entity_id: weather.forecast_home
        state: "sunny"
      - condition: state
        entity_id: person.marco
        state: "not_home"
    action:
      - service: lawn_mower.start_mowing
        target:
          entity_id: lawn_mower.landroid
```

**Was passiert hier?**
- Jeden Morgen um 8:00 Uhr prüft Home Assistant die Bedingungen
- Nur wenn das Wetter sonnig ist UND niemand zu Hause ist, startet der Mäher
- Bei Regen, Wolken oder wenn die Familie im Garten ist: Der Landroid bleibt in der Garage

### Weitere Automatisierungen, die sich lohnen
- **Regen-Stopp:** Wenn es anfängt zu regnen, sofort `lawn_mower.dock` aufrufen (zurück zur Ladestation)
- **Fehler-Benachrichtigung:** Push-Nachricht aufs Handy, wenn der Landroid steckenbleibt oder einen Fehler meldet
- **Akkustand:** Warnung, wenn der Akku unter 20% fällt und der Mäher nicht an der Station ist

## Was ist mit dem ACS Ultraschall-Sensor?

Ich habe den [ACS-Sensor (WA0860) *]({{ site.data.amazon.landroid_acs.url }}) nachgerüstet. Ehrliche Meinung: Naja. Er erkennt Bäume, Mauern und Gartenbänke ganz okay. Aber dünne Hindernisse wie Blumentöpfe, Spielzeug oder den Gartenschlauch, den jemand liegen gelassen hat? Übersieht er fröhlich. Das ist leider eine Hardware-Limitation des Ultraschalls – da wird auch kein Firmware-Update was dran ändern.

Mein Tipp: Spar dir den Frust und investier lieber in ordentliche Rasenkanten aus Metall oder Stein (ca. 10cm hoch) um Beete und empfindliche Bereiche. Das ist zuverlässiger als jeder Sensor und kostet weniger Nerven.

## Fazit

Die drei Mods zusammen haben meinen Landroid ehrlich gesagt erst richtig brauchbar gemacht:
- **Traktor-Räder:** Endlich Traktion, auch wenn es morgens noch feucht ist
- **6-Klingen-Scheibe:** Saubereres Schnittbild, keine Streifen mehr
- **Home Assistant:** Der Mäher fährt nur noch, wenn es auch Sinn macht

Und das Beste: Die 3D-Druck-Mods kosten nur ein paar Euro Filament. Wer keinen eigenen Drucker hat – fragt mal im lokalen Maker-Space oder bei Nachbarn mit Drucker.

*Du hast noch keinen 3D-Drucker? In meinem Artikel zur [USB-Kamera am Neptune 4]({% post_url 2026-03-05-neptune-4-usb-kamera %}) zeige ich dir, wie du deinen Drucker smart überwachst.*

<style>
  .preview-img { display: none !important; }
</style>
