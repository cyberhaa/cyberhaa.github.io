---
layout: post
title: "Raspberry Pi Live-Backup: SD-Karte im laufenden Betrieb klonen mit piclone_cmd"
date: 2024-05-21
categories: [Hardware]
tags: [Backup, Tutorial]
image:
  path: /assets/img/posts/raspberry-pies.jpg
---

Wer sein Smart Home auf einem [Raspberry Pi]({{ site.data.amazon.raspberry_pi_5.url }}) betreibt, lebt gefährlich. SD-Karten sind nicht für 24/7-Schreibzugriffe (Log-Dateien!) gemacht. Irgendwann kommt der Tag, an dem nichts mehr geht – und meistens hat man dann kein aktuelles Backup zur Hand.

Das Problem: Die meisten Tools verlangen, dass du den Pi ausschaltest und die Karte am PC klonst. Nervig, zeitaufwendig und das Smart Home ist währenddessen offline.

Hier kommt **piclone_cmd** ins Spiel.

## Warum piclone_cmd?
Das Tool ist die Kommandozeilen-Version des bekannten "SD Card Copier". Der Clou: Es erstellt einen **Live-Dump** auf eine zweite, per USB angeschlossene SD-Karte oder SSD, während dein System (z. B. Home Assistant) einfach weiterläuft.

* **Hot-Backup:** Kein Herunterfahren nötig.
* **Resize-Support:** Die Ziel-Karte kann größer oder kleiner als die Quelle sein.
* **Headless:** Funktioniert perfekt über SSH.

## Vorbereitung: Die Hardware
Damit das Backup sauber durchläuft, empfehle ich einen ordentlichen USB-Kartenleser. Billig-Adapter sorgen oft für Abbruchfehler mitten im Klonvorgang.

![Bild meiner Pies](/assets/img/posts/raspberry-pies.jpg){: .shadow .rounded-10 }
_Meine beiden Raspberry Pis_

Für ein zuverlässiges Backup nutze ich dieses Setup:
* **Kartenleser:** [SanDisk MobileMate USB 3.0]({{ site.data.amazon.usb_reader_sd.url }})
* **Backup-Karte:** [SanDisk High Endurance microSD]({{ site.data.amazon.sd_card.url }}) (hält deutlich mehr Schreibzyklen aus)

---

## So geht's: Installation & Backup

Zuerst klonen wir das Repository direkt auf den Pi:

```bash
sudo apt update && sudo apt install git -y
git clone https://github.com/nwright-mcc/piclone_cmd.git
cd piclone_cmd
```

### 1. Ziel-Laufwerk finden
Steck deinen USB-Stick oder Kartenleser ein. Jetzt müssen wir herausfinden, welchen Namen das System dem Teil gegeben hat:

```bash
lsblk
```

Deine interne SD-Karte (von der du bootest) heißt fast immer `mmcblk0`. Das Ziel-Laufwerk am USB-Port wird meistens als `sda` oder `sdb` angezeigt. 

**WICHTIG:** Schau dir die Größe an, um sicherzugehen. Wenn du die falsche Platte angibst, löscht das Tool gnadenlos alles auf diesem Laufwerk!

### 2. Den Klon starten
Jetzt wird das Backup auf das Ziel-Laufwerk geschrieben (wir nehmen hier im Beispiel an, dass dein Ziel `/dev/sda` ist):

```bash
sudo ./piclone_cmd /dev/sda
```

Das Tool erledigt nun den Rest: Es legt die Partitionen auf dem Ziel an, formatiert sie passend und schaufelt die Daten rüber. Je nachdem, wie voll deine SD-Karte ist, dauert das etwa 10 bis 30 Minuten.


## Fazit
`piclone_cmd` ist ein Pflicht-Tool auf jedem Pi. Es ist simpel, braucht keine grafische Oberfläche und rettet dir im Ernstfall den Hintern. Einmal eingerichtet, hast du immer eine physische Kopie deiner SD-Karte parat, die du im Notfall nur umstecken musst.

<style>
  /* Versteckt das Header-Bild ganz oben im Artikel */
  .preview-img { display: none !important; }
</style>