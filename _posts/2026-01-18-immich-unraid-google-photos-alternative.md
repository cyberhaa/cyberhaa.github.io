---
layout: post
title: "Immich auf Unraid: Die selbst gehostete Google Photos Alternative"
date: 2026-01-18
categories: [Server & Netzwerk]
tags: [Unraid, Backup, HomeAssistant]
description: "Immich als Google Photos Ersatz auf Unraid installieren – automatischer Foto-Upload vom Handy, Gesichtserkennung und volle Kontrolle über deine Bilder."
image:
  path: /assets/img/posts/immich-demo.png
---

Google Photos ist bequem. Zu bequem. Man merkt gar nicht, wie 100.000 Familienfotos auf fremden Servern landen, bis Google irgendwann den kostenlosen Speicher streicht oder die Preise erhöht. Oder bis man sich fragt: Will ich wirklich, dass ein Konzern die Gesichter meiner Kinder durch seine KI jagt?

![Immich Web-Interface mit Foto-Timeline und Alben-Übersicht](/assets/img/posts/immich-demo.png){: .shadow .rounded-10 }
_Die Immich Benutzeroberfläche – sieht fast aus wie das Original_

Ich wollte raus aus der Abhängigkeit, aber ohne auf den Komfort zu verzichten. Die Lösung heißt **Immich** – eine Open-Source Google Photos Alternative, die auf deinem eigenen Server läuft. Bei mir als Docker-Container auf [Unraid]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}).

## Was kann Immich?

Immich fühlt sich an wie Google Photos – nur dass alles auf deinem NAS liegt:
- **Automatischer Upload** vom Handy (iOS + Android App)
- **Gesichtserkennung** – gruppiert Fotos nach Personen (lokal, keine Cloud-KI)
- **Karten-Ansicht** – zeigt wo deine Fotos aufgenommen wurden
- **Geteilte Alben** – für Familie und Freunde
- **Volltextsuche** – "Strand", "Hund", "Weihnachten" findet passende Bilder
- **Timeline** – chronologische Ansicht wie bei Google Photos
- **Duplikat-Erkennung** – findet doppelte Bilder

## Installation auf Unraid

Immich läuft als Docker-Stack (mehrere Container: Server, Machine Learning, Datenbank, Redis). In den Unraid Community Applications findest du ein fertiges Template:

1. **Community Applications** → Suche nach "Immich"
2. Template installieren – es legt automatisch alle nötigen Container an
3. Speicherpfad angeben: Ich nutze `/mnt/user/media/library` für die Fotobibliothek
4. Port merken (Standard: 2283)

Nach dem Start erreichst du Immich unter `http://DEINE-UNRAID-IP:2283`. Beim ersten Aufruf legst du einen Admin-Account an.

## Handy-App einrichten

1. Immich-App aus dem App Store / Play Store installieren
2. Server-URL eingeben: `http://DEINE-UNRAID-IP:2283`
3. Mit deinem Account anmelden
4. **Automatischen Upload aktivieren** – ab jetzt landen alle neuen Fotos auf deinem NAS

Die App läuft im Hintergrund und lädt neue Fotos hoch, sobald du im WLAN bist. Genau wie bei Google Photos – nur dass die Bilder auf deiner eigenen [Festplatte *]({{ site.data.amazon.nas_hdd_4tb.url }}){:target="_blank"} landen.

## Gesichtserkennung & Machine Learning

Das Machine-Learning-Modul läuft lokal auf deinem Server. Beim ersten Start analysiert es alle vorhandenen Fotos – das dauert je nach Menge ein paar Stunden. Danach erkennt es:
- **Gesichter** – du kannst Personen benennen und dann nach ihnen suchen
- **Objekte** – "Auto", "Katze", "Essen" etc.
- **Szenen** – "Strand", "Berg", "Stadt"

Alles passiert auf deiner Hardware. Nichts verlässt dein Netzwerk. Auf meinem Ryzen 7 Mini-PC dauert die Analyse eines Fotos etwa 1-2 Sekunden.

## Backup nicht vergessen

Fotos nur auf dem NAS zu haben ist besser als in der Cloud – aber noch nicht sicher genug. Was wenn die Festplatte stirbt? Deshalb sichere ich meine Immich-Bibliothek automatisch auf meine [Hetzner Storage Box]({% post_url 2026-03-28-unraid-backup-rclone-hetzner-verschluesselt %}). Verschlüsselt, off-site, jeden Sonntag Nacht.

## Immich vs. Google Photos: Ehrlicher Vergleich

| Feature | Google Photos | Immich |
|---------|--------------|--------|
| Automatischer Upload | ✅ | ✅ |
| Gesichtserkennung | ✅ | ✅ (lokal) |
| Suche nach Objekten | ✅ | ✅ (lokal) |
| Geteilte Alben | ✅ | ✅ |
| Speicher | 15GB gratis, dann Abo | Unbegrenzt (deine Festplatte) |
| Datenschutz | ❌ (Google KI) | ✅ (alles lokal) |
| Kosten | Ab 2€/Monat für mehr Speicher | Einmalig Hardware |
| Verfügbarkeit unterwegs | ✅ (immer) | Nur mit VPN/Reverse Proxy |

Der einzige echte Nachteil: Von unterwegs (außerhalb deines WLANs) kommst du nur an deine Fotos, wenn du einen VPN oder Reverse Proxy einrichtest. Für mich kein Problem – ich schaue Fotos eh meistens zu Hause an.

## Fazit

Immich ist mittlerweile so gut, dass ich Google Photos nicht mehr vermisse. Die App ist schnell, die Gesichtserkennung funktioniert erstaunlich gut, und ich habe die volle Kontrolle über meine Daten. Kein Abo, kein Speicherlimit, keine KI die meine Familienfotos analysiert.

Wenn du schon einen [Unraid-Server]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) hast, ist die Installation in 10 Minuten erledigt. Und mit dem [verschlüsselten Backup auf Hetzner]({% post_url 2026-03-28-unraid-backup-rclone-hetzner-verschluesselt %}) sind deine Erinnerungen auch gegen den Worst Case abgesichert.

<style>
  /* Versteckt das automatische Header-Bild ganz oben im Artikel */
  .preview-img { display: none !important; }
</style>
