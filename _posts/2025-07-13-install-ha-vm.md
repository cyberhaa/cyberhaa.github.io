---
layout: post
title: "Home Assistant OS als VM in Unraid installieren (inkl. USB Passthrough)"
date: 2025-07-13
categories: [Server & Netzwerk]
tags: [Unraid, HomeAssistant, Zigbee]
description: "Home Assistant OS als VM in Unraid installieren – Schritt für Schritt mit USB Passthrough für Zigbee-Stick und Smart Meter Lesekopf."
image:
  path: /assets/img/posts/unraid-vm-ha.png
---

Nachdem mein [Ryzen Mini-PC]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) lief und Unraid eingerichtet war, stand das wichtigste Projekt an: Der Umzug von Home Assistant. 

In Unraid gibt es im App-Store (Community Applications) hunderte fertige Docker-Container. Auch Home Assistant ist dort vertreten. **Aber Achtung: Nimm für Home Assistant NICHT den Docker-Container!** Wenn ihr Home Assistant als Container laufen lasst, fehlt euch der "Supervisor". Das bedeutet: Keine One-Click-Backups und vor allem **kein Add-on Store**. Um das volle Erlebnis (Home Assistant OS) zu bekommen, müssen wir HA als Virtuelle Maschine (VM) aufsetzen. Das ist in Unraid zum Glück in 5 Minuten erledigt.

## Warum eine VM so genial ist
Neben dem Add-on Store hat die VM noch einen riesigen Vorteil für uns Smart-Home-Bastler: **USB Passthrough**. 
Egal ob mein geliebter [Sonoff Zigbee-Stick *]({{ site.data.amazon.sonoff_zigbee_p.url }}) (mehr dazu in meinem [Zigbee-Stick Vergleich]({% post_url 2025-02-19-top-5-zigbee-sticks-fuer-home-assistant %})) oder der USB-Lesekopf für meinen [smarten Stromzähler]({% post_url 2025-08-11-smartmeter-hichi-in-homeassistant %}) – in den VM-Einstellungen von Unraid setze ich einfach einen Haken bei dem jeweiligen Gerät, und Home Assistant erkennt es sofort, als würde es direkt am Gerät stecken. Kein mühsames Herumschlagen mit Linux-Berechtigungen!

## Schritt 1: Das richtige Image herunterladen
Du brauchst zuerst die virtuelle Festplatte von Home Assistant.
1. Geh auf die offizielle Home Assistant Website unter *Alternative Downloads*.
2. Such nach der Version für **KVM / Proxmox (.qcow2)** und lad die Datei herunter.
3. Entpack die Datei (z.B. mit 7-Zip), bis du eine `.qcow2` Datei habt.
4. Kopier diese Datei auf deinen Unraid-Server in den Share `domains` (z. B. unter `\\IP-Eures-Servers\domains\HomeAssistant\`).

## Schritt 2: Die VM in Unraid anlegen
Wechsel in Unraid in den Reiter **VMs** und klick auf **Add VM**. Wähl hier **Linux** aus. 

Jetzt füllst du die Maske aus:
* **Name:** HomeAssistant
* **CPU Cores:** Gib der VM 2 bis 4 Kerne deines Prozessors.
* **Initial/Max Memory:** 2048 MB (2 GB RAM reichen völlig aus).
* **Machine:** Lasst den Standard (meistens i440fx).
* **BIOS:** Wichtig! Änder das auf **OVMF** (oder OVMF TPM2), HAOS bootet über UEFI.
* **Primary vDisk Location:** Wähl hier "Manual" und such über den kleinen Baum-Button deine `.qcow2` Datei aus, die du in Schritt 1 kopiert hast.
* **Primary vDisk Bus:** VirtIO (für beste Performance).
* **Network Bridge:** br0 (damit HA eine eigene IP-Adresse in eurem Heimnetz bekommt).

![Unraid VM-Einstellungen für Home Assistant OS mit USB Passthrough](/assets/img/posts/unraid-vm-ha.png){: .shadow .rounded-10 }
_Unraid VM-Konfiguration für Home Assistant_

## Schritt 3: Die Magie – USB-Geräte durchreichen
Scroll auf der gleichen Seite ganz nach unten. Hier siehst du eine Liste aller USB-Geräte, die aktuell an eurem Mini-PC stecken. 

Setz einfach einen Haken bei deinem **Zigbee-Stick** (z. B. *Texas Instruments CC2652P*) und deinem **Smart Meter Lesekopf**. 
*Tipp: Wenn du später ein neues USB-Gerät ansteckst, musst du die VM nur kurz stoppen, den neuen Haken setzen und wieder starten.*

## Schritt 4: Starten und Einrichten
Klick unten auf **Create**. Die VM wird nun erstellt und automatisch gestartet. 
Gib Home Assistant etwa 2-3 Minuten Zeit, um das System zu entpacken und das Netzwerk zu initialisieren. 

Öffne dann einen neuen Tab im Browser und gib die IP-Adresse deiner neuen VM gefolgt von Port `8123` ein (z. B. `http://192.168.178.50:8123`). 

**Bäm!** Das bekannte Home Assistant Setup-Fenster begrüßt dich. Du kannst jetzt einfach dein Backup vom alten Raspberry Pi hochladen oder ein komplett frisches System aufsetzen. Der Zigbee-Stick wird direkt unter *Einstellungen -> Geräte & Dienste* erkannt.


<style>
  /* Versteckt das automatische Header-Bild ganz oben im Artikel */
  .preview-img { display: none !important; }
</style>