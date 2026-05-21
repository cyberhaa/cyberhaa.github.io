---
layout: post
title: "Mini-PC vs. Raspberry Pi: Warum ich mein Smart Home auf Unraid umgezogen habe"
date: 2025-06-22
categories: [Server & Netzwerk]
tags: [Unraid, Mini-PC, RaspberryPi, HomeAssistant]
description: "Warum ich meine Raspberry Pis durch einen Ryzen Mini-PC mit Unraid ersetzt habe – mehr Leistung, weniger Chaos, kaum mehr Stromverbrauch."
image:
  path: /assets/img/posts/unraid-ace-hardware.png
---

Fast jedes Smart Home beginnt gleich: Man kauft sich einen Raspberry Pi. So war es bei mir auch. Erst lief darauf nur Home Assistant. Dann wollte ich Pi-hole für werbefreies Internet, vielleicht noch eine kleine Nextcloud und einen MQTT-Broker. 

Das Ende vom Lied? Ich hatte irgendwann zwei Raspberry Pis im Netzwerkschrank hängen. Überall Netzteile, herumfliegende USB-Sticks und die ständige Angst, dass wieder eine SD-Karte den Geist aufgibt (deshalb auch mein [Tutorial zum Live-Backup]({% post_url 2024-05-21-raspberry-pi-live-backup-piclone %})). 

Als ich dann ein richtiges NAS (Netzwerkspeicher) aufbauen wollte, stand ich vor der Wahl: Kaufe ich jetzt den dritten Pi und bastele mir was mit externen Festplatten zusammen? **Nein.** Es war Zeit für ein richtiges Upgrade.

## Das Problem mit den vielen Pis
Versteht mich nicht falsch, der Raspberry Pi ist genial. Aber er hat Grenzen:
1. **Die Kosten:** Ein Pi 4 oder Pi 5 mit ordentlichem Gehäuse, gutem Netzteil und SSD-Adapter kostet mittlerweile fast so viel wie ein gebrauchter Office-PC.
2. **Die Leistung:** Sobald man Kameras in Home Assistant einbinden oder Datenbanken (wie InfluxDB für Strom-Statistiken) laufen lassen will, geht der kleine ARM-Prozessor in die Knie.
3. **Das Chaos:** Mehrere Geräte bedeuten mehrere IP-Adressen, mehrere Updates und mehrere Fehlerquellen.

## Mein Upgrade: Ein Mini-PC als Server
Statt eines klassischen "Intel NUC" habe ich mich für die rote Fraktion (AMD) entschieden. Mein neues Herzstück für das gesamte Haus ist der **[ACEMAGICIAN AM06 Pro *]({{ site.data.amazon.mini_pc_acemagician.url }})**. 

Schaut euch mal diese Eckdaten an, verglichen mit einem Pi:
* **CPU:** AMD Ryzen 7 5700U (8 Kerne, 16 Threads! Das Teil hat Leistung satt.)
* **RAM:** 32GB DDR4
* **Speicher:** 1TB M.2 NVMe SSD

![Unraid Dashboard mit AMD Ryzen 7 5700U Hardware-Übersicht und CPU-Auslastung](/assets/img/posts/unraid-ace-hardware.png){: .shadow .rounded-10 }
_Ein Blick in mein Unraid-Dashboard: Der Ryzen 7 langweilt sich meistens._

Mit dieser Hardware langweilt sich Home Assistant zu Tode. Aber genau das war der Plan, denn jetzt kommt die Software ins Spiel, die alles verändert hat.

## Unraid: Alles auf einer Maschine
Ich wollte nicht einfach Linux installieren und alles per Hand zusammenbasteln. Ich wollte ein System, das meine Daten sicher speichert (NAS) und gleichzeitig meine Smart-Home-Dienste hostet. Die Lösung heißt **Unraid**.

Unraid bootet von einem USB-Stick und läuft komplett im Arbeitsspeicher. Das Geniale daran: 
* **Docker-Container:** Pi-hole, Zigbee2MQTT, Node-RED, ein Plex-Media-Server... alles läuft als sauber getrennter Container. Wenn ich eine App zerschieße, lösche ich den Container und starte ihn neu. Das restliche System läuft einfach weiter.
* **Virtuelle Maschinen (VMs):** Mein Home Assistant läuft jetzt als waschechte VM (Home Assistant OS). Ich kann mit einem Klick einen "Snapshot" (Backup) der ganzen Maschine machen, bevor ich ein großes Update einspiele. Geht was schief? Ein Klick und der alte Zustand ist wieder da.
* **Erweiterbarkeit:** Wenn die 1TB SSD irgendwann voll ist, stecke ich einfach eine weitere Festplatte per USB oder intern an, und Unraid fügt sie meinem Speicherpool hinzu.

## Was ist mit dem Stromverbrauch?
Das war meine größte Sorge. Zwei Raspberry Pis verbrauchen zusammen vielleicht 8-10 Watt im Leerlauf. Ein Mini-PC mit einem 8-Kern Ryzen? 

Überraschung: Moderne Mini-PCs sind extrem effizient. Mein Acemagician zieht im normalen Smart-Home-Alltag (Unraid Idle, VMs und Container laufen) **gerade mal 12 bis 15 Watt**. Das sind im Jahr vielleicht 10-15 Euro mehr Stromkosten im Vergleich zu den Pis – dafür habe ich aber die 20-fache Leistung und endlich alles sicher und zentral an einem Ort.

## Fazit: Wer sollte wechseln?
Wenn du gerade erst mit Home Assistant anfängst, ist ein [Raspberry Pi *]({{ site.data.amazon.raspberry_pi_5.url }}) völlig okay. 

Aber sobald du mehr als nur ein paar Lampen steuern willst, du Überwachungskameras planst oder deine eigenen Daten sicher im Netzwerk hosten möchtest: Tu dir den Gefallen und überspringe die "Mehrere Pis"-Phase. Ein leistungsstarker **[Mini-PC mit Unraid *]({{ site.data.amazon.mini_pc_acemagician.url }})** ist für mich die beste Lösung fürs Smart Home. 

Wenn du wissen willst, welches Betriebssystem am besten auf den Mini-PC kommt, lies meinen [Vergleich von Proxmox, TrueNAS und Unraid]({% post_url 2025-07-03-proxmox-vs-unraid-smart-home-server %}).

<style>
  /* Versteckt das Header-Bild ganz oben im Artikel */
  .preview-img { display: none !important; }
</style>