---
layout: post
title: "Zigbee-Sticks für Home Assistant: Mein Wechsel vom ConBee zum Sonoff"
date: 2025-02-19
categories: [Hardware]
tags: [Zigbee, HomeAssistant, Sonoff, ConBee, Empfehlung]
---

Wer sein Smart Home ernst nimmt, wirft die ganzen proprietären Hubs von Philips Hue, IKEA oder Aqara früher oder später genervt raus. Das Ziel ist klar: **Ein einziger Zigbee-Stick am Home Assistant Server**, der alle Geräte zentral und lokal steuert. Das spart nicht nur Strom und Steckdosen, sondern reduziert auch die Verzögerung beim Schalten extrem.

Aber welchen Stick soll man nehmen? Diese Frage hat mich am Anfang auch beschäftigt – und mich erstmal Geld und Nerven gekostet. Hier sind meine Erfahrungen mit den gängigsten Zigbee-Koordinatoren.

## Mein holpriger Start: Der Phoscon ConBee II
Ich hatte anfangs den [ConBee II]({{ site.data.amazon.zigbee_conbee.url }}) von Dresden Elektronik im Einsatz. Das ist eigentlich ein echter Klassiker und läuft mit der hauseigenen "deCONZ"-Software auch ziemlich einsteigerfreundlich. 

Mein Problem fing aber an, als ich tiefer in die Materie einsteigen und zu **Zigbee2MQTT (Z2M)** wechseln wollte. Z2M ist für mich die absolute Pflicht-Software, weil sie gefühlt jedes noch so exotische China-Gadget sofort unterstützt. Leider hat der ConBee in Kombination mit Z2M bei mir immer wieder rumgezickt. Es gab Verbindungsabbrüche und das Netzwerk lief einfach nicht rund. Er kocht da leider hardwareseitig etwas sein eigenes Süppchen.

## Die Erlösung: Sonoff ZBDongle-P
Nach etlichen Frust-Momenten habe ich den ConBee in Rente geschickt und mir den [Sonoff Zigbee 3.0 USB Dongle Plus (P-Version)]({{ site.data.amazon.sonoff_zigbee_p.url }}) gekauft. Das "P" steht hier für den verbauten Texas Instruments Chip (CC2652P).

Was soll ich sagen? Das war die beste Entscheidung für mein Setup. 
Der Stick ist quasi die inoffizielle Referenz-Hardware für Zigbee2MQTT. Ich habe ihn eingesteckt, das Backup eingespielt und seit Tag 1 läuft das Netzwerk absolut fehlerfrei. Durch die externe Antenne ist die Reichweite zudem gigantisch – selbst Sensoren durch zwei Wände hindurch haben einen super Empfang. Und das Beste: Er kostet meistens nur die Hälfte von dem, was man für einen ConBee hinblättert.

## Was ist mit dem offiziellen SkyConnect?
Oft wird auch der [Home Assistant SkyConnect]({{ site.data.amazon.ha_zigbee_skyconnect.url }}) empfohlen, der direkt von den Home-Assistant-Machern kommt. 

Der Stick ist cool, weil er "Multiprotokoll-fähig" ist, also neben Zigbee auch das neue Matter/Thread sprechen kann. Wer die Standard-Integration **ZHA** in Home Assistant nutzt, bekommt hier echtes Plug-and-Play. Wenn du aber (wie ich) auf Zigbee2MQTT setzt, fährst du mit dem Sonoff-Stick aktuell noch stabiler.

## Es gibt noch einen "Sonoff E" – aufpassen!
Kleiner Hinweis beim Kauf: Es gibt vom Platzhirsch auch noch eine E-Version, den [Sonoff ZBDongle-E]({{ site.data.amazon.sonoff_zigbee_e.url }}). Der sieht fast identisch aus, hat aber den gleichen Chip wie der SkyConnect verbaut. Er ist oft ein paar Euro günstiger, aber mein Rat bleibt: Greift für Z2M zur **P-Version**. Den "E" kann man sich später super als Repeater (Router) ins Haus legen, um das Signal zu verstärken.

---

## Mein Fazit & Ein wichtiger Setup-Tipp
Wenn ihr euer Smart Home aufbaut und Home Assistant mit Zigbee2MQTT nutzen wollt, macht nicht den gleichen Fehler wie ich am Anfang. Kauft euch direkt den **[Sonoff Dongle-P]({{ site.data.amazon.sonoff_zigbee_p.url }})**.

**Ganz wichtig:** Bestellt euch unbedingt ein [kurzes USB-Verlängerungskabel]({{ site.data.amazon.usb_1m_cable.url }}) (ca. 1 Meter) dazu! Steckt den Stick niemals direkt in den USB-Port eures Raspberry Pi oder Mini-PCs. Die USB 3.0 Anschlüsse strahlen extrem stark ab und stören das 2,4 GHz Zigbee-Signal massiv. Mit dem Kabel dazwischen habt ihr sofort ein bombenfestes Netzwerk.
