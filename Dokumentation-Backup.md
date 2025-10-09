# Borgbackup installieren und einstellen
## Planung
### Backupserver (10.42.0.30)
- User backupuser
- SSH aktiviert
- Verzeichnis für Borg-Repos: /srv/borg/repos/vm1
- Borg installiert (um Restores/Mounts zu machen)

### Produktivserver (10.42.0.101)
- Borg installiert
- erstellt SSH-Key (privat bleibt hier, public geht zu Backupserver)
- Systemd-Service + Timer kümmern sich um tägliche Backups
- Borg schiebt Backups via SSH auf Backupserver

## installation
### Borgbackup installieren auf Produktivsystem und Backupserver
```sudo apt install borgbackup -y```

### Backupuser auf Backupserver erstellen
- ```sudo adduser --disabled-password --gecos "" backupuser```
- --disabled-password um einen user ohne Passwort zu erstellen
- --gecos um vollständigen Name, Telefonnummer usw. zu überspringen und leer zu lassen.

### Reposetory für Borg auf Backupserver erstellen
- ```sudo mkdir -p /srv/borg/repos/produktiv```
- -p um alle noch nicht vorhandenen übergeordneten Repos zu erstellen

Backupuser die Besitzerrechte des Ordners geben mit:
- ```sudo chown -R backupuser:backupuser /srv/borg/repos/produktiv```
- chown --> change owner
- -R --> Rekursiv dieser Ordner und alle Unterordner

### SSH-Key einrichten 
- auf dem Produktivserver 
- ```ssh-keygen -t ed25519 -C "borgbackup@produktvserver"```
- bei abfragen nichts eintragen
- -t --> darauf folgt der Verschlüsselungstyp ed25519
- -C --> um einen Komentar hinzuzufügen

Nun muss in die entsprechende Datei navigiert werden, um den Public-Key zu kopieren
- ```cat ~/.ssh/id_ed25519.pub```
- dort die Verschlüsselungsmethode und den Key kopieren und zwischenspeicher
- der Kommentar am ende muss nicht mit kopiert werden

Nun muss auf den Backupserver der Key vür den Backupuser hinterlegt werden:
- dafür eine shell als backupuser öffnen mit: ```sudo -u backupuser -s```
- nun ein directory anlegen mit: ```mkdir -p ~/.ssh```
- nun eine Datei anlegen, in der der Key eingefügt werden muss: ```nano ~/.ssh/authorized_keys```

Nun kann noch geprüft werden ob die SSH-verbindung mt dem Key funktioniert.
Dafür auf dem Produktivserver per SSH als backupuser auf dem Backupserver verbinden:
- ```ssh backupuser@10.42.0.30```
- es darf kein passwort abgefragt werden, dann ist alles richtig eingerichtet


  
