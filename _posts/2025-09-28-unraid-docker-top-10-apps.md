---
layout: post
title: "Meine Top 10 Docker-Apps auf Unraid (und warum jede einzelne läuft)"
date: 2025-09-28
categories: [Server & Netzwerk]
tags: [Unraid, HomeAssistant]
description: "Die 10 besten Docker-Container für Unraid im Smart-Home-Einsatz – von AdGuard über Immich bis Zigbee2MQTT. Mein komplettes Setup."
image:
  path: /assets/img/posts/unraid-docker-apps.jpg
---

Einer der größten Vorteile von [Unraid gegenüber einer Synology]({% post_url 2025-10-05-unraid-vs-synology-warum-kein-fertiges-nas %}) ist die Docker-Unterstützung. Über die Community Applications installierst du Container mit einem Klick – kein docker-compose, kein Terminal. Einfach Template auswählen, Pfade eintragen, starten.

Hier sind die 10 Container die auf meinem [Mini-PC]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) 24/7 laufen – und warum ich auf keinen davon verzichten will.

## 1. AdGuard Home + Unbound + Redis

**Was es tut:** Netzwerkweiter Werbeblocker mit eigenem DNS-Resolver und Cache.

Seit ich [AdGuard auf Unraid]({% post_url 2025-08-12-adguard-unbound %}) laufen habe, ist das Internet im ganzen Haus werbefrei – auf jedem Gerät, ohne App, ohne Browser-Extension. Der Smart-TV zeigt keine Werbung mehr, die Kids sind vor Malware-Seiten geschützt, und Unbound sorgt dafür, dass kein DNS-Anbieter mein Surfverhalten mitliest.

**RAM:** ~150MB | **Unverzichtbar:** Ja

## 2. Zigbee2MQTT

**Was es tut:** Verbindet alle Zigbee-Geräte mit Home Assistant über MQTT.

Über 40 Sensoren, Schalter und Lampen laufen darüber. Der Container ist winzig, läuft seit über einem Jahr ohne Neustart und unterstützt quasi jedes Zigbee-Gerät das es gibt. Warum ich [Z2M statt ZHA]({% post_url 2025-11-16-zigbee2mqtt-vs-zha-vergleich %}) nutze, habe ich separat erklärt.

**RAM:** ~80MB | **Unverzichtbar:** Ja

## 3. Mosquitto (MQTT Broker)

**Was es tut:** Der Nachrichtenbus zwischen all meinen Diensten.

Zigbee2MQTT, [OpenDTU]({% post_url 2026-05-10-balkonkraftwerk-opendtu-home-assistant %}), ESPHome-Geräte – alles spricht über MQTT miteinander. Mosquitto ist der Postbote dazwischen. Winzig, stabil, braucht quasi keine Ressourcen.

**RAM:** ~10MB | **Unverzichtbar:** Ja

## 4. Immich

**Was es tut:** Google Photos Ersatz mit automatischem Handy-Upload und Gesichtserkennung.

Alle Familienfotos landen automatisch auf meinem Server statt bei Google. Die App fühlt sich fast identisch an, die Gesichtserkennung läuft lokal, und ich zahle kein Abo. Ausführlich beschrieben in meinem [Immich-Artikel]({% post_url 2026-01-18-immich-unraid-google-photos-alternative %}).

**RAM:** ~2-4GB (wegen Machine Learning) | **Unverzichtbar:** Ja

## 5. AgentDVR

**Was es tut:** Lokaler Netzwerk-Videorekorder für Überwachungskameras.

Meine Reolink-Kameras streamen direkt in AgentDVR. Bewegungserkennung, Zeitplan-Aufnahme, und über Home Assistant bekomme ich Push-Benachrichtigungen mit Snapshot wenn sich jemand an der Haustür bewegt. Alles ohne Cloud-Abo. Details im [Kamera-Artikel]({% post_url 2025-09-14-ueberwachungskamera-lokal-home-assistant-agentdvr %}).

**RAM:** ~500MB | **Unverzichtbar:** Ja

## 6. UniFi Controller

**Was es tut:** Zentrale Verwaltung meines [UniFi-Netzwerks]({% post_url 2026-05-24-unifi-netzwerk-smart-home %}).

Alle Access Points, Switches und Firewall-Regeln werden hierüber konfiguriert. Läuft als Docker statt auf dem Dream Router selbst – so habe ich mehr Kontrolle und kann einfacher Backups machen.

**RAM:** ~500MB (Java...) | **Unverzichtbar:** Ja

## 7. Plex Media Server

**Was es tut:** Streaming-Server für Filme und Serien im Heimnetz.

Filme auf dem NAS, gestreamt auf jeden Fernseher im Haus. Plex transkodiert bei Bedarf (der Ryzen 7 schafft locker 3 gleichzeitige 4K-Streams) und hat Apps für quasi jedes Gerät.

**RAM:** ~300MB (mehr bei Transcoding) | **Unverzichtbar:** Nein, aber nice-to-have

## 8. Vaultwarden

**Was es tut:** Selbst gehosteter Passwort-Manager (Bitwarden-kompatibel).

Alle Passwörter der Familie liegen auf meinem Server statt bei einem Cloud-Anbieter. Die Bitwarden-Apps funktionieren damit ganz normal – Browser-Extension, Handy-App, Auto-Fill. Und wenn Bitwarden morgen die Preise erhöht, juckt mich das nicht.

**RAM:** ~30MB | **Unverzichtbar:** Ja

## 9. Nginx Proxy Manager

**Was es tut:** Reverse Proxy mit Let's Encrypt SSL-Zertifikaten.

Damit erreiche ich meine Dienste von unterwegs über HTTPS mit eigener Subdomain. Home Assistant, Immich, Vaultwarden – alles sicher erreichbar ohne VPN. Die Web-UI macht die Konfiguration zum Kinderspiel.

**RAM:** ~50MB | **Unverzichtbar:** Für Fernzugriff ja

## 10. Watchtower

**Was es tut:** Aktualisiert Docker-Container automatisch.

Einmal am Tag prüft Watchtower ob es neue Images gibt und aktualisiert die Container automatisch. Kein manuelles Klicken mehr. Für kritische Container (Home Assistant, Immich) habe ich das deaktiviert – da update ich lieber manuell nach einem Backup.

**RAM:** ~20MB | **Unverzichtbar:** Nein, aber spart Zeit

## Bonus: Home Assistant als VM

Home Assistant läuft bei mir nicht als Docker-Container, sondern als [vollwertige VM]({% post_url 2025-07-13-install-ha-vm %}). Der Grund: Als VM bekommst du Home Assistant OS mit dem Add-on Store und One-Click-Backups. Als Container fehlt dir der Supervisor und damit viele Komfort-Features.

## Gesamtverbrauch

Alle 10 Container + die Home Assistant VM zusammen:
- **RAM:** ~8-10GB von 32GB belegt
- **CPU:** Im Idle 2-5% (der Ryzen 7 langweilt sich)
- **Strom:** 12-15W für den ganzen Server

Da ist noch massig Luft nach oben. Und genau das ist der Punkt: Mit einem [Mini-PC und Unraid]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) hast du einen Server der all das gleichzeitig kann – für den Preis einer Synology die nur Dateien speichert.

<style>
  .preview-img { display: none !important; }
</style>
