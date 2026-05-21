---
layout: post
title: "Werbefrei & Sicher: Mein DNS-Setup mit AdGuard Home & Unbound auf Unraid"
date: 2025-08-12
categories: [Server & Netzwerk]
tags: [Unraid, AdGuard, Sicherheit]
description: "Netzwerkweiter Werbeblocker mit Kindersicherung: AdGuard Home, Unbound und Redis als Docker-Container auf Unraid einrichten."
image:
  path: /assets/img/posts/adguard-uebersicht.png
---

Wer kennt es nicht: Man macht den teuren Smart-TV an und wird im Menü direkt mit Werbebannern zugekleistert. Man surft auf dem Handy und klickt versehentlich auf einen unsichtbaren Werbe-Layer. Oder noch schlimmer: Die Kinder klicken auf dubiose Links, die Malware nachladen.

Früher habe ich auf jedem Gerät einzeln Adblocker installiert. Heute erledigt mein [Unraid-Server]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) das zentral für das *komplette* Haus – vollautomatisch für jedes WLAN-Gerät, ohne dass ich irgendwo eine App installieren muss. 

Mein Geheimnis? Die Unraid-App **adguardhome-unbound-redis**.



## Warum dieses 3-in-1 Paket?
Viele kennen "Pi-hole" als Netzwerk-Werbeblocker. AdGuard Home ist die moderne, aufgeräumtere Alternative. In den Unraid "Community Applications" gibt es einen speziellen Docker-Container, der gleich drei geniale Tools vereint:

1. **AdGuard Home:** Das ist die Kommandozentrale. Hier trage ich meine Blocklisten ein und sehe in schicken Graphen, welches Gerät im Haus gerade wohin telefoniert.
2. **Unbound:** Das ist ein eigener, lokaler DNS-Resolver. Normalerweise weiß dein Internetanbieter (oder Google/Cloudflare), welche Webseiten du aufrufst. Unbound umgeht diese Mittelsmänner und fragt direkt bei den obersten Stammservern des Internets an. Maximale Privatsphäre!
3. **Redis:** Eine In-Memory-Datenbank. Sie merkt sich (cacht) extrem schnell die IP-Adressen der aufgerufenen Webseiten. Das Ergebnis: Webseiten laden spürbar flotter.

![AdGuard Home Dashboard mit DNS-Statistiken und blockierten Anfragen](/assets/img/posts/adguard-uebersicht.png){: .shadow .rounded-10 }
_Adguard Übersicht_

## Meine Lieblings-Features im Alltag

### 1. Radikale Blocklisten (Tschüss, Smart-TV-Werbung!)
AdGuard Home funktioniert wie ein Türsteher. Wenn mein Samsung- oder LG-Fernseher versucht, die Adresse `ads.samsung.com` aufzurufen, sagt AdGuard: *"Nö, kenn ich nicht"* und blockt die Anfrage (DNS-Sinkhole). Der Fernseher lädt blitzschnell weiter, nur eben ohne das Werbebanner.
Ich habe Blocklisten für alles Mögliche abonniert:
* **Malware & Phishing:** Blockiert bekannte Betrugsseiten, bevor sie überhaupt laden.
* **Microsoft Telemetrie:** Stoppt das ständige Nach-Hause-Telefonieren von Windows 11.
* **Smart Home Tracker:** Verhindert, dass billige China-Gadgets ungefragt Daten ins Internet senden.

### 2. Die eingebaute Kindersicherung (Parental Control)
Das ist für Familien ein absoluter Gamechanger. Mit einem Klick kann ich in AdGuard bestimmte Dienste (wie TikTok, YouTube, Tinder oder Roblox) für das komplette Netzwerk oder nur für bestimmte Geräte (z.B. das Tablet der Kinder) sperren. 
Ich kann sogar die "Safe Search" (Sichere Suche) für Google und Bing netzwerkweit erzwingen, damit keine nicht-jugendfreien Inhalte in den Suchergebnissen auftauchen.

![AdGuard Home Dienste sperren – TikTok, YouTube und andere Dienste blockieren](/assets/img/posts/adguard-dienste-sperren.png){: .shadow .rounded-10 }
_Adguard schnelles Sperren von Webseiten und Diensten_

### 3. Sperrzeiten festlegen
Gute Nacht, Internet! Ich habe eingestellt, dass bestimmte Unterhaltungs-Dienste auf den Geräten der Kids ab 20:00 Uhr einfach nicht mehr aufgelöst werden. Das WLAN bleibt an, Hausaufgaben-Recherchen auf Wikipedia funktionieren noch, aber Social Media streikt.

## Wie kommt der Traffic zu AdGuard?
Die Einrichtung ist simpler, als man denkt. Sobald der Docker-Container auf Unraid läuft, musst du nur in deinen Router (z. B. die Fritz!Box oder UniFi Dream Machine) gehen. 
Dort änderst du in den DHCP-Einstellungen den **lokalen DNS-Server** auf die IP-Adresse deines Unraid-Servers. 

Sobald sich Handys, Laptops oder Saugroboter nun neu mit dem WLAN verbinden, bekommen sie vom Router gesagt: *"Wenn du eine Webseite suchst, frag ab sofort den Unraid-Server!"*

## Fazit
Dieses Setup ist für mich der Hauptgrund, warum ein 24/7 Heimserver unverzichtbar ist. Das Internet fühlt sich an allen Geräten (auch in den In-App-Browsern auf dem Smartphone!) spürbar sauberer und schneller an.

Falls du noch keinen Heimserver hast: In meinem Artikel [Mini-PC vs. Raspberry Pi]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) erkläre ich, warum sich der Umstieg lohnt.


<style>
  /* Versteckt das automatische Header-Bild ganz oben im Artikel */
  .preview-img { display: none !important; }
</style>