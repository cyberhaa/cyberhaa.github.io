---
layout: post
title: "Unraid Backup: Alle Daten verschlüsselt auf eine Hetzner Storage Box sichern (rclone)"
date: 2026-03-28
categories: [Server & Netzwerk]
tags: [Unraid, Backup, Sicherheit]
description: "Verschlüsseltes Off-Site-Backup für Unraid mit rclone und einer Hetzner Storage Box – automatisch, günstig und DSGVO-konform."
image:
  path: /assets/img/posts/hetzner-backup-log.png
---

Ein NAS im Keller ist großartig. Aber was passiert, wenn es brennt, ein Blitz einschlägt oder ein Einbrecher den ganzen Netzwerkschrank mitnimmt? Dann sind nicht nur die Smart-Home-Configs weg, sondern auch 20 Jahre Familienfotos. Darüber will man nicht nachdenken – muss man aber.

Die Lösung heißt **Off-Site-Backup**: Eine verschlüsselte Kopie aller wichtigen Daten, die physisch woanders liegt. Ich nutze dafür [rclone](https://rclone.org/) (Open Source, kostenlos) zusammen mit einer [Hetzner Storage Box](https://www.hetzner.com/storage/storage-box/). Das Ganze läuft vollautomatisch jeden Sonntag Nacht auf meinem [Unraid-Server]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}), und ich muss mich um nichts kümmern.

## Warum Hetzner Storage Box?

- **Günstig:** Ab ca. 3,50€/Monat für 1TB, Rechenzentrum in Deutschland
- **DSGVO-konform:** Daten liegen in deutschen Rechenzentren
- **SFTP/SSH-Zugang:** Perfekt für rclone, kein proprietäres Protokoll
- **Snapshots:** Hetzner kann serverseitig Snapshots erstellen (zusätzliche Sicherheit)

Alternativen wie Backblaze B2 oder Wasabi funktionieren auch mit rclone, aber für uns in Deutschland ist Hetzner preislich und rechtlich einfach die naheliegendste Wahl.

## Das Konzept: Doppelte Verschlüsselung

Mein Setup hat zwei Schichten:

1. **Transport-Verschlüsselung:** Die Verbindung zu Hetzner läuft über SFTP (SSH). Niemand kann die Daten unterwegs mitlesen.
2. **Datei-Verschlüsselung:** rclone verschlüsselt jede Datei *bevor* sie hochgeladen wird (AES-256). Selbst Hetzner kann eure Daten nicht lesen. Auch die Dateinamen werden verschlüsselt.

Das bedeutet: Selbst wenn jemand Zugang zu eurer Storage Box bekommt, sieht er nur unlesbaren Datenmüll. Hetzner selbst übrigens auch – die haben keine Ahnung, was da liegt. Genau so will ich das.

## Schritt 1: rclone auf Unraid installieren

rclone ist in den meisten Unraid-Versionen bereits vorinstalliert. Prüf das kurz über das Terminal:

```bash
rclone version
```

Falls nicht vorhanden, installier das **NerdTools**-Plugin über die Community Applications und aktivier dort rclone.

## Schritt 2: SSH-Key erstellen

Wir authentifizieren uns bei Hetzner nicht mit einem Passwort, sondern mit einem SSH-Key. Das ist sicherer und ermöglicht automatische Backups ohne Passwort-Eingabe.

```bash
mkdir -p /boot/config/ssh
ssh-keygen -t ed25519 -f /boot/config/ssh/hetzner_box_key -N ""
```

Den öffentlichen Key musst du jetzt bei Hetzner hinterlegen:

```bash
cat /boot/config/ssh/hetzner_box_key.pub
```

Kopier die Ausgabe und füg sie im [Hetzner Robot Panel](https://robot.hetzner.com/) unter deiner Storage Box → SSH-Keys ein.

## Schritt 3: rclone konfigurieren

Du brauchst zwei "Remotes" in rclone: Einen für die SFTP-Verbindung und einen für die Verschlüsselung.

```bash
rclone config
```

### Remote 1: SFTP-Verbindung

```
Name: hetzner
Type: sftp
Host: uXXXXXX.your-storagebox.de
User: uXXXXXX
Port: 23
Key file: /boot/config/ssh/hetzner_box_key
Shell type: none
```

*(Deine Zugangsdaten findest du im Hetzner Robot Panel unter der Storage Box.)*

### Remote 2: Verschlüsselungs-Layer

```
Name: hetzner-crypt
Type: crypt
Remote: hetzner:backup
Filename encryption: standard
Directory name encryption: true
Password: (ein langes, sicheres Passwort eingeben)
```

> ⚠️ **Dieses Passwort ist der Schlüssel zu euren Daten!** Ohne dieses Passwort sind alle Backups auf Hetzner unwiederbringlich verloren. Ich meine das ernst: Speichert es in eurem Passwort-Manager, druckt es aus und legt den Zettel irgendwo hin, wo ihr ihn im Notfall findet.

## Schritt 4: Testen

Prüf zuerst, ob die Verbindung steht:

```bash
rclone lsd hetzner:
```

Dann teste einen kleinen Upload mit Verschlüsselung:

```bash
echo "Backup-Test" > /tmp/testfile.txt
rclone copy /tmp/testfile.txt hetzner-crypt:test --progress
```

Kontrollier auf Hetzner, dass die Datei verschlüsselt angekommen ist:

```bash
# So sieht Hetzner die Datei (verschlüsselt):
rclone ls hetzner:backup

# So siehst du die Datei (entschlüsselt):
rclone ls hetzner-crypt:test
```

## Schritt 5: Backup-Script erstellen

Jetzt bauen wir das eigentliche Backup-Script. Ich sichere folgende Daten:

| Was | Warum |
|-----|-------|
| `/boot/config` | Unraid-Konfiguration (USB-Stick) |
| `/mnt/user/appdata` | Alle Docker-Configs (AdGuard, Immich, UniFi etc.) |
| `/mnt/user/pictures` | Familienfotos |
| Home Assistant VM | Das komplette Smart Home |

Erstell ein Script unter `/boot/config/plugins/user.scripts/scripts/hetzner-backup/script`:

```bash
#!/bin/bash
echo "=== Hetzner Backup gestartet: $(date) ==="

# Unraid Config
rclone sync /boot/config hetzner-crypt:flash-config --progress

# Docker App-Daten
rclone sync /mnt/user/appdata hetzner-crypt:appdata --progress

# Fotos
rclone sync /mnt/user/pictures hetzner-crypt:pictures --progress

# Home Assistant VM-Image
rclone copy /mnt/user/isos/haos_ova-14.2.qcow2 hetzner-crypt:ha-vm --progress

echo "=== Hetzner Backup abgeschlossen: $(date) ==="
```

**Wichtig:** Ich nutze `rclone sync` für die meisten Ordner (synchronisiert = löscht auf Hetzner, was lokal nicht mehr existiert). Für die HA-VM nutze ich `rclone copy` (kopiert nur, löscht nichts), weil ich dort auch ältere Versionen behalten möchte.

## Schritt 6: Automatisierung per User Script

1. In Unraid: **Settings → User Scripts**
2. Neues Script anlegen: "hetzner-backup"
3. Den Inhalt von oben einfügen
4. Zeitplan setzen: z.B. `0 3 * * 0` (jeden Sonntag um 3:00 Uhr nachts)

![Unraid User Script Log mit erfolgreichem rclone Backup auf Hetzner Storage Box](/assets/img/posts/hetzner-backup-log.png){: .shadow .rounded-10 }
_So sieht ein erfolgreicher Backup-Lauf im Unraid User Script Log aus_

## Wiederherstellung im Notfall

Falls der schlimmste Fall eintritt und du alles neu aufsetzen musst:

```bash
# rclone auf einem beliebigen Rechner installieren
# (macOS: brew install rclone, Linux: curl https://rclone.org/install.sh | sudo bash)

# Config anlegen (gleiche Zugangsdaten + gleiches Passwort wie oben)
rclone config

# Alles herunterladen
rclone copy hetzner-crypt: ./restore --progress

# Oder nur bestimmte Ordner
rclone copy hetzner-crypt:pictures ./pictures --progress
rclone copy hetzner-crypt:appdata ./appdata --progress
```

> ⚠️ Für die Wiederherstellung immer `rclone copy` nutzen, niemals `rclone sync`! Sonst synchronisiert rclone den leeren lokalen Ordner nach Hetzner und löscht dort alles.

## Fazit

Für ein paar Euro im Monat hast du ein vollautomatisches, verschlüsseltes Off-Site-Backup deines kompletten Unraid-Servers. Hetzner sieht nur verschlüsselten Datenmüll, die Übertragung läuft über SSH, und im Notfall kannst du von jedem Rechner der Welt deine Daten wiederherstellen. Einmal einrichten, nie wieder drüber nachdenken.

Klar, man hofft, dass man das Backup nie braucht. Aber wenn doch, ist man verdammt froh, dass es da ist.

*Noch keinen Unraid-Server? In meinem Artikel [Mini-PC vs. Raspberry Pi]({% post_url 2025-06-22-mini-pc-vs-pi-unraid-switch %}) erkläre ich, warum sich der Umstieg lohnt. Und in [Proxmox vs. TrueNAS vs. Unraid]({% post_url 2025-07-03-proxmox-vs-unraid-smart-home-server %}) vergleiche ich die Betriebssysteme.*

<style>
  .preview-img { display: none !important; }
</style>
