---
title: Installation-DIVERA
parent: Übersicht
---

# DIVERA 24/7 Monitor App auf Raspberry Pi 5

Diese Anleitung beschreibt die Installation und Konfiguration der **DIVERA 24/7 Monitor App** auf einem Raspberry Pi 5 inklusive Autostart, System-Benachrichtigungen und Druckeranbindung.

Quelle: https://help.divera247.com/pages/viewpage.action?pageId=119865769

---

## Voraussetzungen

- Raspberry Pi 5 mit Raspberry Pi OS 64 Bit (Desktop)
- Internetverbindung
- Zugriff auf ein Terminal
- Lokaler Benutzer mit grafischer Oberfläche (z.B. "firealsheim")

---

## 1. SD-Karte vorbereiten

- Raspberry Imager herunterladen: https://www.raspberrypi.com/software/
- Raspberry Pi OS (64 Bit) Desktop auswählen und auf SD-Karte schreiben
- WLAN konfigurieren
- User anlegen (z.B. "firealsheim")
- Raspberry Pi mit SD-Karte starten und Ersteinrichtung durchführen

## 2. System aktualisieren

```bash
sudo apt update
sudo apt upgrade -y
sudo apt dist-upgrade -y
sudo apt autoremove -y
sudo reboot
```


## 3. Monitor-App herunterladen

### 3.1 Verzeichnis für die App anlegen

```bash
mkdir ~/divera
cd ~/divera
```

### 3.2 Monitor-App herunterladen

Datei wird heruntergeladen und umbenannt in `Monitor.AppImage`

```bash
wget -O ~/divera/Monitor.AppImage https://s3.florian.divera247.de/public/software/monitor/DIVERA247-Monitor-2.1.0-arm64.AppImage
```

### 3.2 Monitor-App ausführbar machen

Damit die Monitor-App auf dem Raspberry Pi gestartet werden kann, muss das AppImage ausführbar gemacht werden.

```bash
sudo chmod a+x /pfad/zum/Monitor.AppImage
```

---

## 4. System-Benachrichtigungen aktivieren

Damit die App System-Benachrichtigungen anzeigen kann, muss ein Benachrichtigungsdienst installiert und konfiguriert werden.


### 4.1 D-Bus-Konfigurationsdatei erstellen

```bash
sudo nano /usr/share/dbus-1/services/org.freedesktop.Notifications.service
```

### 4.2 Inhalt der Konfigurationsdatei

```ini
[D-BUS Service]
Name=org.freedesktop.Notifications
Exec=/usr/lib/notification-daemon/notification-daemon
```

---

## 5. Bildschirmschoner bei Alarm deaktivieren

Um den Bildschirmschoner bei einem Alarm deaktivieren zu können, wird folgendes Paket benötigt:

```bash
sudo apt install xdotool
```

---

## 6. Sprachausgabe einrichten

Um die Sprachausgabe auf einem Linux System zu aktivieren muss das Paket libttspico-utils installiert werden.

```bash
sudo apt install libttspico-utils
```

---

## 7. Autostart einrichten

### 7.1 Autostart konfigurieren

#### Autostart-Verzeichnis anlegen

```bash
mkdir -p ~/.config/autostart
```

#### Autostart-Datei erstellen

```bash
sudo nano ~/.config/autostart/divera.desktop
```

#### Inhalt der Autostart-Datei

> ⚠️ Hinweis: Der Dateiname darf **keine Leerzeichen** enthalten und der Pfad zur App muss korrekt angepasst werden.

```ini
[Desktop Entry]
Type=Application
Exec=sh -c "sleep 10 && /Pfad/Zum/Monitor.AppImage"
```

---

## 8. Drucker einrichten (CUPS)

### 8.1 Benötigte Pakete installieren

```bash
sudo apt update
sudo apt install cups printer-driver-brlaser avahi-daemon
sudo systemctl enable cups
sudo systemctl start cups
```

---

### 8.2 Benutzer für das Web-Interface freigeben

```bash
sudo usermod -aG lpadmin firealsheim
```

> 🔁 Hinweis: Ggf. ist eine Ab- und erneute Anmeldung erforderlich.

---

### 8.3 CUPS-Konfiguration anpassen

#### Konfigurationsdatei öffnen

```bash
sudo nano /etc/cups/cupsd.conf
```

#### Einstellungen prüfen oder ergänzen

```conf
Port 631
Listen 0.0.0.0:631

<Location />
  Allow all
</Location>

<Location /admin>
  Allow all
</Location>
```

#### CUPS neu starten

```bash
sudo systemctl restart cups
```

---

### 8.4 CUPS-Weboberfläche aufrufen

Die Weboberfläche ist anschließend erreichbar unter:

```text
http://<IP-des-Pi>:631
```

---

## Hinweise

- Für einen stabilen Autostart empfiehlt sich ein angeschlossener Monitor per HDMI
- Energiesparoptionen des Raspberry Pi sollten ggf. deaktiviert werden
- Updates der Monitor-App sollten die Datei `Monitor.AppImage` nicht umbenennen

---

## Lizenz / Hinweis

DIVERA 24/7 ist ein Produkt der DIVERA GmbH. Diese Anleitung dient ausschließlich der technischen Unterstützung bei der Einrichtung.

