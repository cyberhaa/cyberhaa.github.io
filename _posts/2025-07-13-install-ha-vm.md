---
layout: post
title: "Home Assistant OS als VM in Unraid installieren (inkl. USB Passthrough)"
date: 2025-07-13
categories: [Software]
tags: [Unraid, HomeAssistant, Tutorial, Setup, Zigbee]
image:
  path: /assets/img/posts/unraid-vm-ha.png
---

Nachdem mein [Ryzen Mini-PC]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) lief und Unraid eingerichtet war, stand das wichtigste Projekt an: Der Umzug von Home Assistant. 

In Unraid gibt es im App-Store (Community Applications) hunderte fertige Docker-Container. Auch Home Assistant ist dort vertreten. **Aber Achtung: Nehmt für Home Assistant NICHT den Docker-Container!** Wenn ihr Home Assistant als Container laufen lasst, fehlt euch der "Supervisor". Das bedeutet: Keine One-Click-Backups und vor allem **kein Add-on Store**. Um das volle Erlebnis (Home Assistant OS) zu bekommen, müssen wir HA als Virtuelle Maschine (VM) aufsetzen. Das ist in Unraid zum Glück in 5 Minuten erledigt.

## Warum eine VM so genial ist
Neben dem Add-on Store hat die VM noch einen riesigen Vorteil für uns Smart-Home-Bastler: **USB Passthrough**. 
Egal ob mein geliebter [Sonoff Zigbee-Stick]({{ site.data.amazon.sonoff_zigbee_p.url }}) oder der USB-Lesekopf für meinen smarten Stromzähler (Smart Meter) – in den VM-Einstellungen von Unraid setze ich einfach einen Haken bei dem jeweiligen Gerät, und Home Assistant erkennt es sofort, als würde es direkt am Gerät stecken. Kein mühsames Herumschlagen mit Linux-Berechtigungen!

## Schritt 1: Das richtige Image herunterladen
Wir brauchen zuerst die virtuelle Festplatte von Home Assistant.
1. Geht auf die offizielle Home Assistant Website unter *Alternative Downloads*.
2. Sucht nach der Version für **KVM / Proxmox (.qcow2)** und ladet die Datei herunter.
3. Entpackt die Datei (z.B. mit 7-Zip), bis ihr eine `.qcow2` Datei habt.
4. Kopiert diese Datei auf euren Unraid-Server in den Share `domains` (z. B. unter `\\IP-Eures-Servers\domains\HomeAssistant\`).

## Schritt 2: Die VM in Unraid anlegen
Wechselt in Unraid in den Reiter **VMs** und klickt auf **Add VM**. Wählt hier **Linux** aus. 

Jetzt füllen wir die Maske aus:
* **Name:** HomeAssistant
* **CPU Cores:** Gebt der VM 2 bis 4 Kerne eures Prozessors.
* **Initial/Max Memory:** 2048 MB (2 GB RAM reichen völlig aus).
* **Machine:** Lasst den Standard (meistens i440fx).
* **BIOS:** Wichtig! Ändert das auf **OVMF** (oder OVMF TPM2), HAOS bootet über UEFI.
* **Primary vDisk Location:** Wählt hier "Manual" und sucht über den kleinen Baum-Button eure `.qcow2` Datei aus, die ihr in Schritt 1 kopiert habt.
* **Primary vDisk Bus:** VirtIO (für beste Performance).
* **Network Bridge:** br0 (damit HA eine eigene IP-Adresse in eurem Heimnetz bekommt).

![Unraid Apps](/assets/img/posts/unraid-vm-ha.png){: .shadow .rounded-10 }
_Unraid Apps - Home Assistant VM_

## Schritt 3: Die Magie – USB-Geräte durchreichen
Scrollt auf der gleichen Seite ganz nach unten. Hier seht ihr eine Liste aller USB-Geräte, die aktuell an eurem Mini-PC stecken. 

Setzt einfach einen Haken bei eurem **Zigbee-Stick** (z. B. *Texas Instruments CC2652P*) und eurem **Smart Meter Lesekopf**. 
*Tipp: Wenn ihr später ein neues USB-Gerät ansteckt, müsst ihr die VM nur kurz stoppen, den neuen Haken setzen und wieder starten.*

## Schritt 4: Starten und Einrichten
Klickt unten auf **Create**. Die VM wird nun erstellt und automatisch gestartet. 
Gebt Home Assistant etwa 2-3 Minuten Zeit, um das System zu entpacken und das Netzwerk zu initialisieren. 

Öffnet dann einen neuen Tab im Browser und gebt die IP-Adresse eurer neuen VM gefolgt von Port `8123` ein (z. B. `http://192.168.178.50:8123`). 

**Bäm!** Das bekannte Home Assistant Setup-Fenster begrüßt euch. Ihr könnt jetzt einfach euer Backup vom alten Raspberry Pi hochladen oder ein komplett frisches System aufsetzen. Der Zigbee-Stick wird direkt unter *Einstellungen -> Geräte & Dienste* erkannt.


<style>
  /* Versteckt das automatische Header-Bild ganz oben im Artikel */
  .preview-img { display: none !important; }
</style>