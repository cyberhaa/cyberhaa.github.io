---
layout: post
title: "Strom sparen mit Home Assistant: Standby-Killer und smarte Verbrauchskontrolle"
date: 2026-04-05
categories: [Smart Home]
tags: [HomeAssistant, Solar, SmartMeter]
description: "Mit Home Assistant Stromfresser finden, Standby-Verbrauch eliminieren und den Eigenverbrauch von Solar maximieren – konkrete Automatisierungen."
---

Strom kostet in Deutschland mittlerweile über 30 Cent pro Kilowattstunde. Da lohnt es sich, genauer hinzuschauen. Und genau das kann Home Assistant richtig gut: Messen, visualisieren und automatisch abschalten, was gerade nicht gebraucht wird.

Ich habe mit ein paar smarten Steckdosen und meinem [Smart Meter Lesekopf]({% post_url 2025-08-11-smartmeter-hichi-in-homeassistant %}) angefangen – und war ehrlich gesagt schockiert, was manche Geräte im Standby ziehen. Hier sind meine konkreten Maßnahmen, die zusammen über 150€ im Jahr sparen.

## Schritt 1: Messen – Wo geht der Strom hin?

Bevor du optimieren kannst, musst du wissen wo der Strom hinfließt. Dafür brauchst du:
- Einen [Smart Meter Lesekopf]({% post_url 2025-08-11-smartmeter-hichi-in-homeassistant %}) für den Gesamtverbrauch
- [Smarte Steckdosen mit Strommessung *]({{ site.data.amazon.shelly_plug_s.url }}){:target="_blank"} an verdächtigen Geräten

Steck die Steckdosen an alles, was 24/7 läuft: TV, Soundbar, Spielkonsole, PC-Monitor, Drucker. Lass das eine Woche laufen und schau dir dann im Home Assistant Energy Dashboard die Zahlen an.

**Meine größten Überraschungen:**
- TV + Soundbar im Standby: 18W (= 47€/Jahr für nichts)
- Gaming-PC im "Aus"-Zustand: 12W (= 31€/Jahr)
- Drucker im Standby: 8W (= 21€/Jahr)

## Schritt 2: Standby-Killer mit smarten Steckdosen

Die einfachste Maßnahme: Geräte die nicht gebraucht werden, komplett vom Strom trennen. Mit einem [Shelly Plug S *]({{ site.data.amazon.shelly_plug_s.url }}){:target="_blank"} geht das automatisch.

**Automatisierung: TV-Ecke aus wenn niemand schaut**

```yaml
automation:
  - alias: "TV Standby-Killer"
    trigger:
      - platform: state
        entity_id: media_player.wohnzimmer_tv
        to: "off"
        for: "00:10:00"
    action:
      - service: switch.turn_off
        target:
          entity_id: switch.tv_steckdose
```

10 Minuten nachdem der TV ausgeschaltet wird, trennt die smarte Steckdose den kompletten TV-Bereich (TV, Soundbar, Fire Stick) vom Strom. Wenn ich den TV wieder einschalten will, schalte ich die Steckdose per Sprachbefehl oder Automatisierung wieder ein.

## Schritt 3: Geräte nur bei Solar-Überschuss laufen lassen

Wer ein [Balkonkraftwerk]({% post_url 2026-05-10-balkonkraftwerk-opendtu-home-assistant %}) hat, will den produzierten Strom möglichst selbst verbrauchen statt ihn für 8 Cent einzuspeisen. Home Assistant kann Verbraucher gezielt dann einschalten, wenn Überschuss da ist.

**Beispiel: Handy-Ladung nur bei Sonne**

```yaml
automation:
  - alias: "Laden bei Solar-Überschuss"
    trigger:
      - platform: numeric_state
        entity_id: sensor.stromzaehler_aktuelle_leistung
        below: -100
    action:
      - service: switch.turn_on
        target:
          entity_id: switch.ladestation_steckdose
```

Wenn der Stromzähler negativ wird (= du speist ein), schaltet sich die Ladestation ein. Wird der Überschuss weniger, geht sie wieder aus. Funktioniert genauso für: Akku-Staubsauger laden, Laptop laden, Powerbank füllen.

## Schritt 4: Verbrauch visualisieren

Das Home Assistant Energy Dashboard zeigt dir auf einen Blick:
- Tages-, Wochen- und Monatsverbrauch
- Kosten in Euro (Strompreis hinterlegbar)
- Welches Gerät wie viel verbraucht (wenn du Steckdosen mit Messung hast)
- Solar-Produktion vs. Verbrauch

Allein die Visualisierung ändert schon das Verhalten. Wenn du siehst, dass der Trockner 3€ pro Durchgang kostet, hängst du die Wäsche öfter auf die Leine.

## Schritt 5: Zeitbasierte Optimierung

Manche Geräte müssen laufen – aber nicht unbedingt zur teuersten Zeit. Mit einem dynamischen Stromtarif (z.B. Tibber) und Home Assistant kannst du Verbraucher in günstige Stunden verschieben:

- **Spülmaschine:** Startet nachts um 2:00 wenn der Strom am günstigsten ist
- **E-Auto laden:** Nur zwischen 1:00 und 5:00 Uhr
- **Warmwasser-Boiler:** Aufheizen wenn Solar-Überschuss da ist oder Strom billig ist

## Was spart das konkret?

| Maßnahme | Ersparnis/Jahr |
|----------|---------------|
| TV-Ecke Standby eliminieren | ~47€ |
| Gaming-PC komplett aus | ~31€ |
| Drucker nur bei Bedarf | ~21€ |
| Waschmaschine bei Solar-Überschuss | ~20€ |
| Licht-Automatisierung (nicht vergessen auszuschalten) | ~30€ |
| **Gesamt** | **~150€** |

Und das sind nur die offensichtlichen Sachen. Je mehr du misst, desto mehr findest du.

## Fazit

Home Assistant ist nicht nur ein Spielzeug für Technik-Nerds – es spart echtes Geld. Die Kombination aus Strommessung, smarten Steckdosen und Automatisierungen macht Schluss mit unsichtbarem Standby-Verbrauch. Und wer ein Balkonkraftwerk hat, maximiert damit den Eigenverbrauch.

Die Investition (ein paar Shelly Plugs für je 15€) hat sich bei mir in weniger als 3 Monaten amortisiert.

*Grundlage für alles: Ein [Smart Meter Lesekopf]({% post_url 2025-08-11-smartmeter-hichi-in-homeassistant %}) für den Gesamtverbrauch und ein [Balkonkraftwerk mit OpenDTU]({% post_url 2026-05-10-balkonkraftwerk-opendtu-home-assistant %}) für die Produktion.*

<style>
  .preview-img { display: none !important; }
</style>
