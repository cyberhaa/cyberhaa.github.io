---
layout: post
title: "Proxmox vs. TrueNAS vs. Unraid: Mein Weg zum perfekten Smart-Home-OS"
date: 2025-07-03
categories: [Server & Netzwerk]
tags: [Unraid, Proxmox, HomeAssistant]
description: "Proxmox vs. TrueNAS vs. Unraid im Vergleich: Welches Betriebssystem eignet sich am besten als Smart-Home-Server für zu Hause?"
image:
  path: /assets/img/posts/unraid-apps.png
---

Mein neuer [Ryzen Mini-PC *]({{ site.data.amazon.mini_pc_acemagician.url }}){:target="_blank"} stand endlich auf dem Schreibtisch. Die Raspberry Pis waren abgebaut, die Hardware war bereit. Fehlte nur noch eine "Kleinigkeit": Welches Betriebssystem installiere ich da eigentlich drauf?

Ich wollte ein NAS (Netzwerkspeicher) für meine Daten und gleichzeitig eine sichere, isolierte Umgebung für Home Assistant, Pi-hole und meine anderen Smart-Home-Dienste. 

Wer in Foren oder auf YouTube nach dem besten "Home Server OS" sucht, landet unweigerlich bei den großen Drei: **Proxmox, TrueNAS und Unraid**. Hier ist die ehrliche Zusammenfassung, warum ich mich am Ende ganz bewusst gegen die kostenlosen Platzhirsche und für Unraid entschieden habe.

## Der heiße Kandidat: Proxmox VE
Proxmox ist ein sogenannter Hypervisor. Es ist kostenlos, extrem mächtig und in der Tech-Community absoluter Standard. Es tut im Grunde nichts anderes, als deine Hardware in virtuelle Maschinen (VMs) und Container (LXC) aufzuteilen.

**Warum es rausflog:**
Proxmox ist genial für Virtualisierung, aber **es ist von Haus aus kein NAS**. Wenn man Festplatten im Netzwerk freigeben will (SMB/NFS), muss man sich das entweder auf Kommandozeilen-Ebene selbst in Debian basteln oder eine TrueNAS-VM in Proxmox virtualisieren und Festplatten durchreichen. Das war mir für mein Zuhause einfach eine Nummer zu komplex und wartungsintensiv. Ich wollte weniger Bastel-Chaos, nicht mehr.

## Der Speicher-König: TrueNAS Scale
TrueNAS basiert auf dem Dateisystem ZFS. Das ist quasi der heilige Gral für Datensicherheit im Enterprise-Bereich. Zudem bietet die "Scale"-Version auch Docker-Container und VMs an.

**Warum es rausflog:**
ZFS ist wahnsinnig unflexibel, wenn es um Erweiterungen geht. Man kann nicht einfach eine alte 2TB-Platte und eine neue 8TB-Platte zusammen in einen Pool werfen. Die Festplatten müssen gleich groß sein, und man erweitert Speicherpools in "vdevs" (Gruppen). Für ein großes Firmen-NAS super – für mein Zuhause, wo ich vielleicht später einfach mal wahllos eine externe USB-Festplatte anstöpseln will, völlig ungeeignet. Zudem braucht ZFS extrem viel RAM.

## Der Gewinner: Unraid
Unraid kostet zwar Geld (es gibt je nach Version verschiedene Lizenzen), aber nach der 30-tägigen Testversion war für mich klar: Das ist jeden Cent wert. 

Hier sind die Gründe, die für mich den Ausschlag gegeben haben:

### 1. Bootet vom USB-Stick
Unraid installiert man nicht auf der internen Festplatte. Es bootet von einem handelsüblichen USB-Stick und lädt sich komplett in den Arbeitsspeicher (RAM). 
Der riesige Vorteil: Meine schnelle 1TB NVMe SSD im Mini-PC ist komplett frei für meine virtuellen Maschinen und Docker-Container. Das System blockiert keinen wertvollen Speicherplatz.

### 2. Festplatten mischen, wie man will
Der Name "Unraid" ist Programm (es nutzt kein klassisches RAID). Wenn ich später mehr Speicher brauche, kaufe ich einfach irgendeine Festplatte, egal wie groß, stecke sie an, und mein Speicherpool wird größer. Das Dateisystem (XFS/BTRFS/ZFS) speichert die Daten so ab, dass ich bei einem Ausfall nur die Daten der defekten Platte verliere, nicht den ganzen Pool (und mit einer Parity-Platte verliere ich gar nichts).

### 3. Der "Community Applications" App Store
Das ist der absolute Gamechanger für Smart-Home-Nutzer. Unraid hat einen eingebauten "App Store" für Docker-Container. 
Du willst Zigbee2MQTT, Node-RED, Plex oder AdGuard installieren? Klick auf Installieren, trag deine Ordner-Pfade in ein schickes Web-Interface ein, und der Container läuft. Kein händisches Tippen von "docker-compose.yaml" Dateien im Terminal.

![Unraid Community Applications App Store mit Docker-Containern](/assets/img/posts/unraid-apps.png){: .shadow .rounded-10 }
_Unraid Apps - Hier wird man fündig_

### 4. Virtuelle Maschinen leicht gemacht
Home Assistant als Docker-Container laufen zu lassen, schränkt oft Funktionen (wie die Add-ons) ein. In Unraid habe ich Home Assistant OS (HAOS) mit wenigen Klicks als echte, voll isolierte Virtuelle Maschine aufgesetzt. Wenn ich ein Backup meines Smart Homes brauche, kopiere ich einfach die VM-Datei.

## Fazit
Proxmox ist toll für Leute, die primär tüfteln und virtualisieren wollen. TrueNAS ist für Daten-Puristen. 

**Unraid ist für mich das perfekte Schweizer Taschenmesser.** Es nimmt einem die lästige Konfigurationsarbeit unter Linux ab, vereint NAS und Smart-Home-Server in einer genialen Weboberfläche und spart mir am Ende das Wichtigste: **Zeit.** Zeit, die ich lieber in neue Home Assistant Automatisierungen stecke, als in die Reparatur meiner Server-Struktur.

Im nächsten Schritt zeige ich dir, wie du [Home Assistant als VM in Unraid aufsetzt]({% post_url 2025-07-13-install-ha-vm %}) – inklusive USB Passthrough für deinen Zigbee-Stick.

---

<style>
  /* Versteckt das automatische Header-Bild ganz oben im Artikel, falls du eins hinzufügst */
  .preview-img { display: none !important; }
</style>