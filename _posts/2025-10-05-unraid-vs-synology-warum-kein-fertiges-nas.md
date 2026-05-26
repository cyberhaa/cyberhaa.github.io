---
layout: post
title: "Unraid vs. Synology: Warum ich kein fertiges NAS kaufe"
date: 2025-10-05
categories: [Server & Netzwerk]
tags: [Unraid, Mini-PC]
description: "Unraid auf einem Mini-PC vs. Synology DiskStation – ehrlicher Vergleich für Smart-Home-Nutzer. Warum Selbstbau flexibler und günstiger ist."
image:
  path: /assets/img/posts/unraid-vs-synology.jpg
---

Wenn man "NAS kaufen" googelt, landet man unweigerlich bei Synology. Die DiskStations sind überall empfohlen, sehen schick aus und versprechen einfache Einrichtung. Ich hab trotzdem keins gekauft – und stattdessen Unraid auf einen [Mini-PC]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) gepackt. Hier ist warum.

## Was Synology gut kann

Erstmal fair bleiben: Synology hat seine Berechtigung.

- **Plug and Play:** Auspacken, Festplatten rein, einschalten, fertig. Die DSM-Oberfläche ist wirklich gut gemacht.
- **Stabil:** Das Ding läuft einfach. Jahrelang. Ohne dass man was anfassen muss.
- **Apps:** Synology Photos, Drive, Surveillance Station – alles vorinstallierbar mit einem Klick.
- **Support:** Wenn was kaputt ist, gibt es einen Hersteller den man anrufen kann.

Für jemanden der einfach nur Dateien im Netzwerk speichern will und sich nicht mit Linux beschäftigen möchte, ist Synology völlig okay.

## Warum ich trotzdem Unraid nehme

### 1. Die Hardware-Falle

Eine [Synology DS224+ *]({{ site.data.amazon.synology_ds224.url }}){:target="_blank"} kostet ~350€. Dafür bekommst du einen Intel Celeron J4125 mit 2GB RAM. Das ist... wenig. Für ein reines NAS reicht es, aber sobald du Docker-Container oder VMs laufen lassen willst (Home Assistant, AdGuard, Immich), geht dem Ding die Puste aus.

Mein [Mini-PC *]({{ site.data.amazon.mini_pc_acemagician.url }}){:target="_blank"} hat für ähnliches Geld einen Ryzen 7 mit 32GB RAM und 1TB NVMe SSD. Das ist die 10-fache Leistung. Und ich kann jederzeit RAM nachrüsten oder eine zweite SSD einbauen.

### 2. Festplatten-Flexibilität

Synology nutzt klassisches RAID. Das heißt: Alle Festplatten sollten gleich groß sein. Wenn du mit 2x 4TB anfängst und später auf 8TB erweitern willst, musst du beide Platten tauschen.

Unraid ist da komplett anders: Du kannst Festplatten beliebiger Größe mischen. Eine 2TB, eine 4TB und eine 8TB? Kein Problem. Einfach reinstecken und der Pool wird größer. Genau das brauche ich – ich kaufe Festplatten wenn sie im Angebot sind, egal welche Größe.

### 3. VMs und Docker ohne Limits

Auf meinem Unraid laufen gleichzeitig:
- [Home Assistant als VM]({% post_url 2025-07-13-install-ha-vm %})
- [AdGuard Home + Unbound]({% post_url 2025-08-12-adguard-unbound %})
- [Immich]({% post_url 2026-01-18-immich-unraid-google-photos-alternative %}) für Fotos
- UniFi Controller
- Zigbee2MQTT
- knxd für [PowerNet]({% post_url 2026-04-24-busch-jaeger-powernet-knxd-home-assistant %})
- Plex Media Server
- Und noch ein Dutzend mehr

Versuch das mal auf einer Synology DS224+ mit 2GB RAM. Geht nicht. Selbst die teureren Modelle (DS923+, ~600€) haben nur einen Celeron und 4GB RAM.

### 4. Kein Vendor Lock-in

Synology DSM ist proprietär. Wenn Synology morgen die Preise verdoppelt oder ein Feature hinter ein Abo packt (Surveillance Station hat schon Lizenz-Limits), bist du gefangen. Deine Daten liegen in einem Synology-Format auf Synology-Hardware.

Unraid ist zwar auch nicht Open Source (Lizenz ~60-130€ einmalig), aber deine Daten liegen auf Standard-Dateisystemen (XFS, BTRFS). Wenn Unraid morgen verschwindet, steckst du die Festplatten in jeden Linux-Rechner und hast Zugriff.

### 5. Community Applications

Der Unraid "App Store" hat hunderte vorkonfigurierte Docker-Container. Ein Klick und der Container läuft – mit Web-UI für die Konfiguration. Das ist genauso einfach wie Synology Package Center, nur mit deutlich mehr Auswahl.

## Der ehrliche Vergleich

| Kriterium | Synology DS224+ | Unraid auf Mini-PC |
|-----------|----------------|-------------------|
| Preis (ohne Festplatten) | ~350€ | ~350€ (Mini-PC + Unraid-Lizenz) |
| CPU | Intel Celeron J4125 | AMD Ryzen 7 5700U |
| RAM | 2GB (max 32GB) | 32GB ab Werk |
| Speicher | 2 Festplatten-Slots | 1TB NVMe + USB/SATA erweiterbar |
| Einrichtung | 10 Minuten | 30-60 Minuten |
| Docker/VMs | Eingeschränkt (wenig RAM) | Unbegrenzt |
| Festplatten mischen | Nein (RAID) | Ja |
| Stromverbrauch | ~15W | ~12-15W (Idle) |
| Lautstärke | Leise (Festplatten hörbar) | Lautlos (nur SSD) |
| Support | Hersteller-Support | Community/Foren |

## Wann Synology trotzdem Sinn macht

Ich will hier nicht dogmatisch sein. Synology ist die richtige Wahl wenn:
- Du dich null mit Linux/Docker beschäftigen willst
- Du nur Dateien speichern und teilen willst (kein Smart Home)
- Du Wert auf Hersteller-Support legst
- Du mehrere große Festplatten im RAID betreiben willst (Synology hat mehr Slots)

## Fazit

Für ein reines NAS ist Synology top. Aber sobald du einen Smart-Home-Server, Foto-Verwaltung, Werbeblocker und Media-Server auf der gleichen Kiste laufen lassen willst, ist ein Mini-PC mit Unraid die deutlich bessere Wahl. Mehr Leistung, mehr Flexibilität, gleicher Preis.

Und ehrlich: Die Einrichtung von Unraid ist auch kein Hexenwerk. Wenn du Home Assistant zum Laufen gebracht hast, schaffst du auch Unraid.

*Wie ich meinen Mini-PC als Unraid-Server eingerichtet habe, steht in meinem [Hardware-Artikel]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}). Und welches Betriebssystem am besten passt, vergleiche ich in [Proxmox vs. TrueNAS vs. Unraid]({% post_url 2025-07-03-proxmox-vs-unraid-smart-home-server %}).*

<style>
  .preview-img { display: none !important; }
</style>
