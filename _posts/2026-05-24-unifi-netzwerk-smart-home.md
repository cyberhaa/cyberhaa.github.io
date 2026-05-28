---
layout: post
title: "UniFi Netzwerk fürs Smart Home: Mein Setup mit Dream Router und U6+"
date: 2026-05-24
categories: [Server & Netzwerk]
tags: [UniFi, HomeAssistant, Sicherheit]
description: "UniFi Netzwerk für ein stabiles Smart Home einrichten – VLANs, IoT-Trennung und lückenloses WLAN mit Dream Router und U6+ Access Points."
image:
  path: /assets/img/posts/unifi-wlan.jpg
---

Wer 50+ Smart-Home-Geräte im Netzwerk hat, merkt irgendwann: Die Fritz!Box stößt an ihre Grenzen. Nicht unbedingt bei der Geschwindigkeit, sondern bei der Kontrolle. Welches Gerät funkt wohin? Warum hängt sich der Zigbee-Hub auf, wenn die Kids Netflix streamen? Und warum sieht die billige China-Kamera mein ganzes Netzwerk?

Ich bin vor einer Weile auf **Ubiquiti UniFi** umgestiegen und bereue nichts. Hier ist mein Setup und warum es für ein Smart Home so viel besser funktioniert als eine Consumer-Box.

![UniFi Dream Router und Netzwerkkomponenten im Einsatz](/assets/img/posts/unifi-wlan.jpg){: .shadow .rounded-10 }
_Mein UniFi-Setup im Flur_

## Warum UniFi?

UniFi ist kein einzelner Router – es ist ein System. Du kaufst dir die Komponenten, die du brauchst, und steuerst alles über eine zentrale Software (den UniFi Controller). Der läuft bei mir als Docker-Container auf meinem [Unraid-Server]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}).

Die Killer-Features für Smart Homes:
- **VLANs:** Du kannst dein Netzwerk in getrennte Bereiche aufteilen. IoT-Geräte kommen in ein eigenes VLAN und sehen deine PCs und NAS nicht.
- **Mehrere SSIDs:** Ein WLAN für die Familie, eins für Gäste, eins für IoT – alles über die gleichen Access Points.
- **Zentrale Verwaltung:** Alle Geräte, Firewall-Regeln und Traffic-Statistiken an einem Ort.
- **PoE:** Access Points werden über das Netzwerkkabel mit Strom versorgt. Kein extra Netzteil nötig.

## Mein Setup

### Der Kern: UniFi Dream Router
Der [UniFi Dream Router *]({{ site.data.amazon.unifi_dream_router.url }}){:target="_blank"} ist mein Einstiegspunkt ins Netzwerk. Er vereint Router, Switch, WiFi 6 Access Point und den UniFi Controller in einem Gerät. Für die meisten Wohnungen reicht der alleine schon aus.

Bei mir steht er im Flur und versorgt Erdgeschoss und Keller. Für den ersten Stock und den Garten habe ich zusätzliche Access Points.

### Access Points: U6+ für drinnen, U6 Mesh für draußen
Im Obergeschoss hängt ein [UniFi U6+ *]({{ site.data.amazon.unifi_u6_plus.url }}){:target="_blank"} an der Decke. Der versorgt Schlafzimmer, Bad und Kinderzimmer mit stabilem WiFi 6. Für den Garten (Landroid, Outdoor-Kameras){:target="_blank"} habe ich einen [U6 Mesh *]({{ site.data.amazon.unifi_u6_mesh.url }}){:target="_blank"} unter dem Dachüberstand montiert.

### Switch: Flex Mini wo nötig
Am Schreibtisch und im Netzwerkschrank stecken [UniFi Flex Mini Switches *]({{ site.data.amazon.unifi_flex_mini.url }}){:target="_blank"}. Die sind winzig, lautlos und werden per USB-C mit Strom versorgt. Perfekt für Stellen, wo man mehrere Geräte per Kabel anbinden will.

## VLANs: IoT-Geräte einsperren

Das ist der eigentliche Grund, warum ich UniFi nutze. Mein Netzwerk ist in drei VLANs aufgeteilt:

| VLAN | Zweck | Geräte |
|------|-------|--------|
| Standard (1) | Familie | Laptops, Handys, NAS |
| IoT (20) | Smart Home | Zigbee-Bridge, Shellys, Kameras, Saugroboter |
| Gast (30) | Besucher | Gäste-WLAN, kein Zugriff auf lokale Geräte |

Die Firewall-Regeln sind simpel:
- IoT-Geräte dürfen **nicht** ins Standard-Netzwerk (können also nicht auf mein NAS oder meine PCs zugreifen)
- Home Assistant (im Standard-VLAN) darf **ins** IoT-VLAN (um die Geräte zu steuern)
- Gäste dürfen nur ins Internet, sonst nichts

So kann die billige China-Kamera zwar ins Internet (für Firmware-Updates), aber sie sieht mein NAS mit den Familienfotos nicht. Und wenn ein IoT-Gerät gehackt wird, ist der Schaden begrenzt.

## UniFi + Home Assistant

Home Assistant erkennt UniFi-Geräte automatisch über die eingebaute **UniFi Network Integration**. Damit bekommst du:
- **Anwesenheitserkennung:** HA weiß, welche Handys im WLAN sind → wer ist zu Hause?
- **Geräte-Tracking:** Wann hat sich welches Gerät zuletzt verbunden?
- **Bandbreiten-Monitoring:** Welches Gerät frisst gerade das meiste Internet?

Die Anwesenheitserkennung nutze ich für Automatisierungen: Licht aus wenn niemand da ist, Heizung runter, [Landroid starten]({% post_url 2026-04-12-worx-landroid-tuning-3d-druck-home-assistant %}).

## Was kostet der Spaß?

UniFi ist nicht billig – aber auch nicht so teuer wie viele denken:
- Dream Router: ~200€
- U6+ Access Point: ~100€
- Flex Mini Switch: ~30€

Für ein Einfamilienhaus mit gutem WLAN überall (inkl. Garten) landest du bei ca. 400-500€. Das klingt nach viel, aber eine Fritz!Box 7590 AX kostet auch schon 250€ – und kann kein VLAN.

## Fazit

Wenn du mehr als eine Handvoll Smart-Home-Geräte hast und dir Sicherheit wichtig ist, führt an einem vernünftigen Netzwerk kein Weg vorbei. UniFi gibt dir die Kontrolle, die eine Consumer-Box einfach nicht bieten kann. Und das zentrale Management über den Controller (der bei mir auf [Unraid als Docker]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) läuft) macht das Ganze erstaunlich pflegeleicht.

*Für werbefreies Internet im ganzen Netzwerk schau dir meinen Artikel zu [AdGuard Home auf Unraid]({% post_url 2025-08-12-adguard-unbound %}) an – das ergänzt sich perfekt mit den UniFi VLANs.*

<style>
  /* Versteckt das automatische Header-Bild ganz oben im Artikel */
  .preview-img { display: none !important; }
</style>

