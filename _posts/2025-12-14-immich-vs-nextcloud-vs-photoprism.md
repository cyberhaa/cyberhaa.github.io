---
layout: post
title: "Immich vs. Nextcloud Photos vs. PhotoPrism: Welche Google Photos Alternative?"
date: 2025-12-14
categories: [Server & Netzwerk]
tags: [Unraid, Backup]
description: "Immich, Nextcloud Photos und PhotoPrism im ehrlichen Vergleich – welche selbst gehostete Foto-Verwaltung ersetzt Google Photos am besten?"
---

Wer seine Fotos von Google Photos befreien will, steht vor der Qual der Wahl. Es gibt mittlerweile drei ernstzunehmende Open-Source-Alternativen: **Immich**, **Nextcloud Photos** und **PhotoPrism**. Alle drei laufen auf dem eigenen Server, alle drei versprechen das Google-Photos-Erlebnis ohne Cloud.

Ich habe alle drei auf meinem [Unraid-Server]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) getestet und bin am Ende bei Immich gelandet. Hier ist warum – und für wen die anderen besser passen.

## Immich: Der Google-Photos-Klon

Immich ist das jüngste der drei Projekte und hat ein klares Ziel: Google Photos so nah wie möglich nachbauen. Und das merkt man.

**Was Immich gut kann:**
- Handy-App mit automatischem Upload (fühlt sich an wie Google Photos)
- Gesichtserkennung und Objekt-Suche (lokal, Machine Learning)
- Timeline-Ansicht, Karten, Erinnerungen ("Vor 3 Jahren...")
- Geteilte Alben für Familie
- Schnell – auch bei 100.000+ Fotos

**Was nervt:**
- Noch in aktiver Entwicklung – Breaking Changes bei Updates möglich
- Kein Datei-Manager (es ist NUR für Fotos/Videos)
- Braucht relativ viel RAM für Machine Learning (~4GB empfohlen)

**Für wen:** Leute die einen 1:1 Google Photos Ersatz wollen und sonst nichts. Familien die eine gemeinsame Fotobibliothek brauchen.

## Nextcloud Photos: Der Alleskönner

Nextcloud ist eigentlich eine komplette Cloud-Plattform (Dateien, Kalender, Kontakte, Office). Photos ist "nur" eine App darin.

**Was Nextcloud Photos gut kann:**
- Fotos sind Teil deiner gesamten Cloud (Dateien, Kalender, alles an einem Ort)
- Automatischer Upload per Handy-App
- Teilen per Link (wie Google Drive)
- Riesiges Ökosystem an Apps und Integrationen
- Recognize-App für Gesichtserkennung (nachinstallierbar)

**Was nervt:**
- Langsam. Wirklich langsam. Bei großen Bibliotheken (50.000+ Fotos) wird die Galerie zur Geduldsprobe
- Die Foto-Ansicht fühlt sich wie ein Dateimanager an, nicht wie eine Foto-App
- Gesichtserkennung ist ein Nachgedanke, nicht so gut wie bei Immich
- Nextcloud selbst ist ein Ressourcen-Fresser (PHP, Datenbank, Cron-Jobs)

**Für wen:** Leute die sowieso schon Nextcloud nutzen und ihre Fotos nicht in einem separaten System haben wollen. Oder wer neben Fotos auch Dateisync, Kalender und Kontakte selbst hosten will.

## PhotoPrism: Der Foto-Nerd

PhotoPrism richtet sich an Leute, die ihre Foto-Sammlung wirklich organisieren wollen. Es ist weniger "Social" und mehr "Archiv".

**Was PhotoPrism gut kann:**
- Beste Suchfunktion der drei (Farben, Orte, Kameras, Objektive, Labels)
- Arbeitet direkt auf deinem bestehenden Ordner-System (keine eigene Datenbank-Struktur)
- Sehr stabil und ausgereift
- Geringer Ressourcenverbrauch im Vergleich zu Immich
- RAW-Support für Fotografen

**Was nervt:**
- Kein automatischer Handy-Upload eingebaut (braucht Syncthing oder ähnliches davor)
- Keine geteilten Alben für Familie
- Eher Einzel-Nutzer-Tool, nicht für Familien designed
- Manche Features nur in der bezahlten "Essentials" Version

**Für wen:** Fotografen mit großen Archiven die ihre bestehende Ordnerstruktur behalten wollen. Einzelpersonen die eine mächtige Suche brauchen.

## Der direkte Vergleich

| Feature | Immich | Nextcloud Photos | PhotoPrism |
|---------|--------|-----------------|------------|
| Handy-App mit Auto-Upload | ✅ Nativ | ✅ Nextcloud App | ❌ Extern nötig |
| Gesichtserkennung | ✅ Sehr gut | ⚠️ Okay (Recognize) | ✅ Gut |
| Geschwindigkeit | ✅ Schnell | ❌ Langsam | ✅ Schnell |
| Geteilte Alben | ✅ | ✅ | ❌ |
| Arbeitet auf bestehenden Ordnern | ❌ Eigene Struktur | ❌ Eigene Struktur | ✅ |
| Ressourcenverbrauch | ⚠️ Hoch (ML) | ⚠️ Hoch (PHP) | ✅ Niedrig |
| Familien-tauglich | ✅ | ⚠️ | ❌ |
| Stabilität | ⚠️ Jung, aktive Entwicklung | ✅ Ausgereift | ✅ Ausgereift |
| Mehr als nur Fotos | ❌ | ✅ (komplette Cloud) | ❌ |

## Mein Fazit

Ich nutze **Immich** – weil es sich am meisten wie Google Photos anfühlt und meine Familie damit klarkommt, ohne dass ich was erklären muss. Die App ist intuitiv, der Upload läuft automatisch, und die Gesichtserkennung gruppiert unsere Fotos nach Personen.

Wer schon Nextcloud hat und keine extra Software installieren will: Nextcloud Photos reicht für den Grundbedarf. Aber erwarte nicht das Google-Photos-Feeling.

Und wer als Fotograf 500.000 RAW-Dateien in einer bestehenden Ordnerstruktur hat: PhotoPrism ist dein Tool.

*Wie du Immich auf Unraid installierst, zeige ich dir Schritt für Schritt in meinem [Immich-Tutorial]({% post_url 2026-01-18-immich-unraid-google-photos-alternative %}).*

<style>
  .preview-img { display: none !important; }
</style>
