---
layout: post
title: "Günstige USB-Kamera am Elegoo Neptune 4 einrichten"
date: 2026-03-05
categories: [3D-Druck]
tags: [3D-Druck, Neptune4, Klipper]
description: "USB-Webcam am Elegoo Neptune 4 einrichten – schwarzes Bild beheben mit dem richtigen Video-Port und YUYV-Modus in mjpg_streamer."
image:
  path: /assets/img/posts/neptune-camera.jpeg
---

Ein 3D-Drucker ohne Kamera ist wie ein Server ohne Netzwerk: Man weiß nie genau, ob im Hintergrund gerade alles abbrennt oder fröhlich vor sich hin schnurrt. 

Als mein Elegoo Neptune 4 Plus endlich lief, wollte ich natürlich sofort eine einfache, günstige No-Name USB-Webcam anschließen, um den Druckfortschritt bequem vom Schreibtisch (oder später übers Smart Home) zu überwachen. 

![USB-Webcam am Elegoo Neptune 4 Plus 3D-Drucker montiert](/assets/img/posts/neptune-camera.jpeg){: .shadow .rounded-10 }
_Meine günstige USB-Kamera am Neptune 4 Plus_

Kamera in den USB-Port gesteckt, das Fluidd/Mainsail-Webinterface geöffnet und... **das Bild bleibt schwarz.** Wenn dir das auch so geht, liegt es meistens daran, dass der Standard-Dienst für die Kamera (`webcamd`) auf dem Neptune 4 nicht weiß, wo das Programm liegt, oder schlichtweg den falschen USB-Port abfragt. Nach einigem Suchen per SSH habe ich das Problem gelöst. Hier ist die Schritt-für-Schritt-Anleitung, wie du jede günstige Kamera zum Laufen bringt.

## 1. Den Standort des Streamers finden
Das Herzstück für das Kamerabild ist ein kleines Programm namens `mjpg_streamer`. Normalerweise liegt das unter Linux an einem Standard-Ort. Auf dem Neptune 4 Plus hat Elegoo das aber im Benutzerverzeichnis versteckt. 

Wenn man sich per SSH (z.B. über Windows PowerShell oder PuTTY) auf dem Drucker einloggt, findet man es hier:
`/home/mks/mjpg-streamer/`

## 2. Den richtigen Video-Port (Device) identifizieren
Wenn man eine Kamera ansteckt, probiert Klipper oft automatisch den Port `/dev/video0` aus. Das führt bei günstigen USB-Kameras am Neptune oft zu der Fehlermeldung *"video capture not supported"*.

**Die Lösung:** Bei der Neptune-Serie ist der korrekte Port für den Videostream fast immer **`/dev/video4`**.

## 3. Der manuelle Testlauf (Proof of Concept)
Bevor wir jetzt wild in den Konfigurationsdateien herumschreiben, testen wir, ob die Kamera mit dem richtigen Port und einem "Allesfresser-Modus" (YUYV) überhaupt ein Bild liefert. 

Logg dich per SSH auf dem Drucker ein und gebt diesen Befehl ein (alles in einer Zeile):

```bash
cd /home/mks/mjpg-streamer && LD_LIBRARY_PATH=. ./mjpg_streamer -i "input_uvc.so -d /dev/video4 -r 640x480 -f 10 -y" -o "output_http.so -w ./www"
```

**Was machen diese Parameter?**
* `-d /dev/video4`: Sagt dem Streamer, dass er unseren herausgefundenen USB-Port nutzen soll.
* `-y`: Aktiviert den YUYV-Modus. Das ist der absolute Lebensretter für viele günstige No-Name-Cams, die das Standardformat nicht flüssig liefern.
* `-f 10`: Setzt die Framerate auf 10 Bilder pro Sekunde. Das reicht völlig zur Überwachung und schont die CPU des Druckers enorm.
* `-r 640x480`: Die Auflösung. Wenn eure Kamera mehr schafft (z.B. `1280x720`), könnt ihr das hier anpassen.

Wenn der Befehl ohne Abbruch durchläuft, kannst du in deinem Browser prüfen, ob das Bild da ist: 
`http://[IP-ADRESSE-EURES-DRUCKERS]:8080/?action=stream`

## 4. Die Konfiguration dauerhaft speichern
Damit wir diesen langen Befehl nicht nach jedem Neustart des Druckers neu eintippen müssen, machen wir das Ganze jetzt persistent.

1. Öffne dein **Fluidd-Dashboard** im Browser.
2. Geh links auf den Reiter **Konfiguration** (`{...}`).
3. Such und öffne die Datei **`webcam.txt`**.
4. Pass die Parameter so an, dass sie unserem erfolgreichen Testlauf entsprechen:

```plaintext
camera="usb"
camera_usb_options="-d /dev/video4 -r 640x480 -f 15 -y"
```
*(Tipp: Ich habe die Framerate hier auf 15 erhöht, testet einfach, was euer Drucker und eure Kamera gut verkraften).*

Speicher die Datei oben rechts ab.

## 5. Den Dienst neu starten
Damit der Drucker die geänderte `webcam.txt` auch einliest, müssen wir den Kamera-Dienst einmal neu starten. 

Geh zurück in dein SSH-Terminal und tipp:
```bash
sudo systemctl restart webcamd
```
*(Falls du nach einem Passwort gefragt wirst: Das Standard-Passwort bei Elegoo lautet `makerbase`).*

## Das Ergebnis
Fertig! Der "Neptune-Watcher" läuft. Das Bild ist nun stabil im Netzwerk erreichbar und wird direkt auf der Startseite im Fluidd-Dashboard angezeigt. Nie wieder blind drucken!

![Webcam-Stream eines laufenden 3D-Drucks im Fluidd Dashboard](/assets/img/posts/neptune-camera-print.avif){: .shadow .rounded-10 }
_Die No-Name Kamera liefert jetzt ein sauberes Bild direkt in Fluidd._


<style>
  /* Versteckt das automatische Header-Bild ganz oben im Artikel */
  .preview-img { display: none !important; }
</style>