---
layout: post
title: "Busch-Jäger PowerNet am Leben halten: Mit knxd und Home Assistant ins Smart Home retten"
date: 2026-04-24
categories: [Smart Home]
tags: [KNX, HomeAssistant, Unraid]
description: "Busch-Jäger PowerNet EIB ist abgekündigt und Ersatzteile kaum noch zu bekommen. So bindest du deine Anlage über knxd und Home Assistant ein."
image:
  path: /assets/img/posts/busch-jager-programmer.jpeg
---

Wenn in deinem Haus eine **Busch-Jäger PowerNet EIB** Anlage verbaut ist, kennst du das Problem vermutlich: Das System wurde abgekündigt. Busch-Jäger (mittlerweile ABB) verkauft keine PowerNet-Komponenten mehr. Ersatzteile? Gibt es nur noch gebraucht auf eBay, und selbst da werden die Angebote immer weniger und die Preise immer frecher.

Bei mir war es so: Wir haben ein Haus gekauft (Baujahr ~2011), dem der Vorbesitzer das PowerNet-System hat andrehen lassen. Damals wurde das als modernes Smart Home verkauft – KNX-Bustechnik über die vorhandene Stromleitung (Powerline), ohne extra Busleitung ziehen zu müssen. Klingt erstmal clever, aber heute sitzt du da mit einer Anlage, die du weder erweitern noch reparieren kannst, wenn mal ein Aktor den Geist aufgibt. Und das passiert früher oder später.

![Busch-Jäger PowerNet USB-Programmiergerät 6133 mit USB- und Stromanschluss](/assets/img/posts/busch-jager-programmer.jpeg){: .shadow .rounded-10 }
_Das Busch-Jäger USB-Programmiergerät – unser Tor zum PowerNet-Bus_

Die gute Nachricht: **Du kannst deine PowerNet-Anlage mit Home Assistant verbinden** und so Schritt für Schritt durch moderne Smart-Home-Technik ergänzen oder langfristig ablösen. Der Schlüssel dazu ist das USB-Programmiergerät von Busch-Jäger und die Open-Source-Software **knxd**.

## Das Problem: Home Assistant kann kein PowerNet

Home Assistant hat eine eingebaute KNX-Integration. Die ist richtig gut – aber sie unterstützt ausschließlich **KNX/IP**. Das heißt: Du brauchst ein KNX-IP-Interface oder einen KNX-IP-Router im Netzwerk. Direkt über USB? Geht nicht. Hat mich auch erstmal genervt.

Für klassisches KNX über Twisted-Pair-Kabel (TP) gibt es solche IP-Interfaces von Gira, Weinzierl oder MDT für 150-300€. Aber PowerNet ist kein TP – es ist **Powerline (PL)**. Die Signale laufen über die Stromleitung im Haus. Und KNX-IP-Interfaces für Powerline gibt es quasi nicht mehr zu kaufen.

Was du aber vermutlich noch hast: Das **Busch-Jäger USB-Programmiergerät** (Modell 6133 oder 6123). Das ist eine Unterputz-Dose mit einem USB-Anschluss auf der einen und einem Stromanschluss auf der anderen Seite. Über USB verbindest du dich mit dem Gerät, und das Gerät schickt die KNX-Signale über den Stromanschluss direkt auf die Powerline zu deinen Aktoren und Sensoren. Normalerweise nutzt man das Ding nur zum Programmieren mit der ETS-Software – aber wir zweckentfremden es als permanentes Gateway zu Home Assistant. Funktioniert einwandfrei.

## Die Lösung: knxd als USB-zu-IP-Bridge

**knxd** ist ein Open-Source-Daemon, der ursprünglich als Nachfolger des alten eibd entwickelt wurde. Er kann ein USB-KNX-Interface nehmen und es im Netzwerk als KNX/IP-Interface bereitstellen. Genau das brauchen wir.

Der Datenfluss sieht so aus:

```
PowerNet-Aktoren ←→ Stromleitung (Powerline) ←→ USB-Dose (6133) ←→ knxd ←→ KNX/IP ←→ Home Assistant
```

knxd spricht über USB mit der Programmier-Dose, die wiederum die KNX-Telegramme über die Stromleitung an deine PowerNet-Komponenten schickt und empfängt. Richtung Home Assistant stellt knxd das Ganze als KNX/IP-Interface bereit – und das versteht die HA-Integration problemlos.

## Mein Setup: knxd als VM auf Unraid

Ich lasse knxd als eigene kleine VM auf meinem [Unraid-Server]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) laufen. Die USB-Programmier-Dose hängt per USB-Kabel am Server und wird per [USB Passthrough]({% post_url 2025-07-13-install-ha-vm %}) sauber an die VM durchgereicht. So läuft knxd unabhängig von Home Assistant.

Alternativ kannst du knxd auch direkt auf einem Raspberry Pi oder als Docker-Container betreiben. Für Home Assistant OS gibt es sogar ein [knxd Add-on](https://community.home-assistant.io/t/knxd-add-on-convert-your-knx-usb-interface-into-an-ip-interface-that-can-be-used-by-ha/38108){:target="_blank"}, das die Einrichtung vereinfacht.

### Schritt 1: USB-Gerät identifizieren

Verbinde die Busch-Jäger USB-Dose per USB-Kabel mit deinem Server und prüf, ob sie erkannt wird:

```bash
lsusb
```

Du solltest einen Eintrag mit der Vendor-ID `145c` sehen (Busch-Jäger). Notier dir die Bus- und Device-Nummer:

```
Bus 001 Device 005: ID 145c:1330
```

### Schritt 2: knxd installieren

Auf Debian/Ubuntu:

```bash
sudo apt-get update
sudo apt-get install knxd knxd-tools
```

### Schritt 3: knxd konfigurieren

Die Konfiguration liegt unter `/etc/knxd.conf`. Hier ist meine funktionierende Config für das Busch-Jäger 6133 USB-Interface:

```ini
[main]
addr = 1.1.128
client-addrs = 1.1.200:8
connections = server,A.usb
logfile = /var/log/knxd.log

[A.usb]
driver = usb

[server]
server = knxd_tcp
```

**Was bedeutet das?**
- `addr = 1.1.128` – Die KNX-Adresse, die knxd im Bus bekommt (muss frei sein in eurer ETS-Konfiguration)
- `client-addrs = 1.1.200:8` – Adressen für Clients, die sich über IP verbinden (Home Assistant bekommt eine davon)
- `driver = usb` – knxd sucht automatisch nach dem USB-Interface
- `server = knxd_tcp` – Stellt einen KNX/IP-Server bereit

### Schritt 4: knxd starten und testen

```bash
sudo systemctl enable knxd
sudo systemctl start knxd
```

Prüf, ob knxd läuft und den USB-Stick gefunden hat:

```bash
sudo systemctl status knxd
```

Jetzt kannst du testen, ob du den Bus mitlesen kannst:

```bash
knxtool vbusmonitor1 ip:localhost
```

Wenn ihr jetzt einen Lichtschalter an eurer PowerNet-Anlage drückt, solltest du die KNX-Telegramme im Terminal sehen. Wenn das klappt: Jackpot. Die Brücke steht.

### Schritt 5: Home Assistant verbinden

In Home Assistant gehst du auf **Einstellungen → Geräte & Dienste → Integration hinzufügen → KNX**.

Als Verbindungstyp wählst du **Tunneling** und gibst du die IP-Adresse deines knxd-Servers ein (Port 3671).

Ab jetzt kannst du in der `configuration.yaml` deine PowerNet-Geräte als KNX-Entities anlegen:

```yaml
knx:
  light:
    - name: "Wohnzimmer Decke"
      address: "1/1/1"        # Schaltadresse aus eurer ETS-Konfiguration
      state_address: "1/1/2"  # Statusadresse

  switch:
    - name: "Flur Steckdose"
      address: "1/2/1"

  climate:
    - name: "Heizung Wohnzimmer"
      temperature_address: "2/1/1"
      target_temperature_state_address: "2/1/2"
      setpoint_shift_address: "2/1/3"
```

Die Gruppenadressen findest du in deinem ETS-Projekt. Falls du das Projekt nicht mehr hast: Mit `knxtool groupsocketlisten ip:localhost` kannst du den Bus belauschen und durch Betätigen der Schalter die Adressen herausfinden.

## Die Langfrist-Strategie: Schrittweise ablösen

Das Schöne an dieser Lösung: Du musst nicht alles auf einmal umbauen. Home Assistant kennt jetzt deine PowerNet-Geräte und du kannst sie ganz normal in Automatisierungen einbinden.

Wenn dann irgendwann ein PowerNet-Aktor stirbt und auf eBay keiner mehr aufzutreiben ist, ersetzt du genau diesen einen Aktor durch was Modernes:

- **Lichtschalter:** Shelly 1 hinter den bestehenden Schalter klemmen (WLAN, lokal steuerbar)
- **Dimmer:** Shelly Dimmer 2 oder Zigbee-Dimmer (z.B. über einen [Zigbee-Stick]({% post_url 2025-02-19-top-5-zigbee-sticks-fuer-home-assistant %}))
- **Rollladenaktoren:** Shelly 2PM oder Zigbee-Rollladenaktoren
- **Heizungssteuerung:** Zigbee-Thermostate (z.B. Aqara E1)

Home Assistant ist dabei der zentrale Punkt, der sowohl die alten PowerNet-Geräte als auch die neuen Zigbee/WLAN-Geräte unter einem Dach vereint. Für die Bewohner ändert sich nichts – die Automatisierungen laufen einfach weiter, egal ob das Signal über Powerline oder Zigbee kommt.

## Wichtige Hinweise

- **ETS-Projekt sichern!** Falls du noch Zugang zur ETS-Software hast, exportiere dein Projekt und speichere es an einem sicheren Ort. Ohne die Gruppenadressen wird die Einrichtung in Home Assistant deutlich mühsamer.
- **USB-Programmier-Dose aufbewahren!** Das Busch-Jäger 6133/6123 ist dein einziger Zugang zum PowerNet-Bus. Ohne dieses Gerät ist die Anlage nicht mehr programmierbar und nicht mehr an Home Assistant anbindbar. Wenn du ein zweites auf eBay findest – kauf es als Ersatz.
- **knxd und ETS gleichzeitig:** Du kannst knxd und die ETS-Software nicht gleichzeitig auf die USB-Dose zugreifen lassen. Wenn du Änderungen in der ETS machen willst, stoppe knxd vorher.

## Fazit

PowerNet ist tot – aber deine Anlage muss es nicht sein. Mit knxd als Brücke zwischen dem USB-Programmiergerät und Home Assistant holst du dir die volle Kontrolle zurück. Du kannst deine bestehenden Schalter, Dimmer und Aktoren weiter nutzen und gleichzeitig Sprachsteuerung, Automatisierungen und Fernzugriff nachrüsten.

Und wenn nach und nach Komponenten ausfallen, tauschst du sie einfach gegen günstige Zigbee- oder WLAN-Alternativen aus – ohne die ganze Anlage auf einmal rausreißen zu müssen. So habe ich mir das vorgestellt, als wir das Haus gekauft haben. Nur dass der Weg dahin etwas holpriger war als gedacht.

*Wie du Home Assistant als VM auf Unraid aufsetzt und USB-Geräte durchreichst, erkläre ich in meinem [Unraid VM Tutorial]({% post_url 2025-07-13-install-ha-vm %}). Und falls du noch nach dem richtigen Zigbee-Stick für die neuen Geräte suchst: [Hier ist mein Vergleich]({% post_url 2025-02-19-top-5-zigbee-sticks-fuer-home-assistant %}).*

<style>
  .preview-img { display: none !important; }
</style>
