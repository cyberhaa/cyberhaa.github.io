---
layout: post
title: "Stromzähler smart machen: Live-Verbrauch & Solar-Einspeisung in Home Assistant"
date: 2025-08-11
categories: [Smart Home]
tags: [HomeAssistant, Solar, SmartMeter]
description: "Stromzähler per IR-Lesekopf auslesen und Live-Verbrauch plus Solar-Einspeisung im Home Assistant Energy Dashboard anzeigen."
image:
  path: /assets/img/posts/ha-energie-diagram.png
---

Wer eine Solaranlage auf dem Dach (oder ein Balkonkraftwerk am Geländer) hat, kennt das Problem: Die App des Wechselrichters zeigt zwar an, was *produziert* wird. Aber wie viel Strom zieht das Haus gerade wirklich? Und wie viel von meinem kostbaren Solarstrom verschenke ich gerade an den Netzbetreiber?

Um das herauszufinden, müssen wir direkt an die Quelle: Unseren Hauptstromzähler im Keller. 

In diesem Artikel zeige ich dir, wie ich meinen digitalen Stromzähler (Smart Meter) mit einem winzigen Lesekopf auslese und die Live-Daten direkt in Home Assistant hole. 

## Die Hardware: Hichi USB IR Lesekopf

<div class="img-right" markdown="1">
![Hichi USB IR Lesekopf am digitalen Stromzähler befestigt](/assets/img/posts/smartmeter.jpg){: .shadow .rounded-10 }
_USB Smartmeter am Zähler_
</div>

Moderne digitale Stromzähler haben auf der Vorderseite zwei kleine optische Dioden. Darüber funken sie im Sekundentakt per Infrarot den aktuellen Zählerstand und den Live-Verbrauch in die Welt hinaus. Man sieht das Blinken nicht mit dem bloßen Auge (haltet mal die Handykamera davor!), aber man kann es auslesen.

Dafür habe ich mir den **[SmartMeterReader USB, IR Lesekopf (Optokopf - UART) *]({{ site.data.amazon.smart_meter_usb.url }}){:target="_blank"}** gekauft. 
Er kostet nicht viel, hat einen integrierten Magneten und "klebt" damit von ganz alleine exakt über der Infrarot-Schnittstelle des Zählers. 


*Hinweis: Es gibt den Hichi auch als WLAN-Version (mit ESP8266). Da mein [Unraid-Server]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) aber ohnehin direkt im Keller neben dem Zählerschrank steht, habe ich mich ganz bewusst für die stabile USB-Variante entschieden. Kein WLAN-Abbruch, keine Verzögerung.*

## Schritt 1: Den Zähler freischalten (WICHTIG!)
Standardmäßig senden die Stromzähler in Deutschland aus Datenschutzgründen nur volle Kilowattstunden (kWh) und verstecken den genauen Live-Verbrauch. 

Damit der Zähler im Detail mit euch redet, brauchst du die **PIN deines Stromzählers**. 
1. Ruf bei deinem Netzbetreiber an (nicht dem Stromanbieter, sondern dem Betreiber der Leitungen vor Ort, z.B. Stadtwerke).
2. Sag, dass du die PIN für euren Zähler (Zählernummer bereithalten) braucht. Die schicken das meistens kostenlos per Post.
3. Wenn die PIN da ist, musst du sie mit einer Taschenlampe (!) in den Zähler blinken. Das genaue Vorgehen steht in der Anleitung eures Zählers. Aktivier dabei zwingend den Menüpunkt **"INFO"** oder **"InF"** auf "ON". Erst dann funkt der Zähler die Nachkommastellen und den Live-Verbrauch!

## Schritt 2: USB Passthrough in Unraid
Den USB-Stecker des Hichi stecken wir nun in unseren Server. Weil ich [Home Assistant als VM in Unraid]({% post_url 2025-07-13-install-ha-vm %}) laufen lasse, ist die Einbindung ein Kinderspiel:
Ich gehe in Unraid auf meine Home Assistant VM, klicke auf *Edit*, scrolle nach unten zu den USB-Geräten und setze den Haken bei dem Hichi-Stick (wird oft als *Silicon Labs CP2102* oder *CH340* erkannt). VM neu starten, fertig. 

## Schritt 3: Die Einbindung in Home Assistant
Die meisten Stromzähler in Deutschland sprechen die Sprache "SML" (Smart Message Language). Home Assistant hat dafür bereits eine fertige, offizielle Integration an Bord!

1. Geh in Home Assistant auf **Einstellungen -> Geräte & Dienste**.
2. Klick unten rechts auf **Integration hinzufügen** und such nach **EDL21**.
3. Als Pfad gibst du den USB-Port ein. Meistens ist das `/dev/ttyUSB0` oder `/dev/ttyUSB1`. 
4. Klick auf Absenden.

Das war's! Home Assistant legt nun automatisch die Sensoren an. Die wichtigsten für unser Energy Dashboard sind:
* **Zählerstand Bezug (meist 1.8.0):** Das ist der Strom, den ihr aus dem Netz kauft.
* **Zählerstand Einspeisung (meist 2.8.0):** Das ist der Solarstrom, den ihr ins Netz fließen lasst.
* **Aktuelle Leistung (W):** Euer absoluter Live-Verbrauch im Haus (wird bei Überschuss durch Solar negativ!).

![Home Assistant Energy Dashboard mit Solar-Einspeisung und Stromverbrauch](/assets/img/posts/ha-energie-diagram.png){: .shadow .rounded-10 }
_Energie Übersicht in Home Assistant_

## Fazit
Für rund 20 Euro und ein bisschen Taschenlampen-Geblinke im Keller hat man sein Energie-Management komplett im Griff. Mit diesen Sensoren im Home Assistant Energy Dashboard sehe ich jetzt auf den Cent genau, wann sich das Waschen lohnt und wie viel meiner Solar-Energie wirklich im Haus bleibt.

*Du willst auch deinen Netzwerk-Traffic im Griff haben? Dann schau dir an, wie du mit [AdGuard Home dein ganzes Netzwerk werbefrei machst]({% post_url 2025-08-12-adguard-unbound %}).*

<style>
  /* Versteckt das automatische Header-Bild ganz oben im Artikel */
  .preview-img { display: none !important; }

  /* Macht, dass das Bild rechts steht und der Text umfließt */
  .img-right {
    float: right;
    margin-left: 1.5rem;
    margin-bottom: 0.5rem;
    max-width: 40%;
    clear: right;
  }

  /* Auf dem Handy wird der Platz zu eng, da machen wir es wieder normal breit */
  @media (max-width: 768px) {
    .img-right {
      float: none;
      display: block;
      margin: 1rem auto;
      max-width: 100%;
    }
  }
</style>