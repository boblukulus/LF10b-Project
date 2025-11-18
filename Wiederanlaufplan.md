# Wiederanlaufplan Projekt-Pterodactyl
## 1. Schritt Ubuntu Server Installation
```JSON
Setup Configuration {
    "Language": "English",
    "Update Installer": True,
    "Keyboard" : "German - German",
    "Install-Type": "Normal",
    "Search 3rd party driver": True,
    "Disk as LVM group" : False,
    "User" : {
        "Full Name" : "LF10b",
        "Servername" : "main",
        "username" : "lf10b",
        "password" : "lf10b"
    },
    "Ubuntu Pro" : False,
    "OpenSSH Server" : True
}
```
### System Update
nach reboot ausführen:
`sudo apt update && sudo apt upgrade -y`

## 2. Installations-Skript ausführen

### Link zum [skript] ()
### installiert SSH, ansible, BorkBackup


## 3. Schritt Ansible-Skript für die Einrichtung

## 4. Schritt EInrichten / installieren von Monitoring
### Es muss nun Graphana und Prometheus installiert werden
### Link zum [Installationsguide](https://github.com/Lucsifer/LF10b-Project/blob/main/Server-config-guide.md#monitoring-23-sept-2025)
### Kurzbeschreibung: 
Der Guide erklärt, wie Node Exporter, Minecraft Exporter, Prometheus und Grafana installiert und verbunden werden.

### Ergebnis:  
- Metriken vom Server & Minecraft werden gesammelt  
- Prometheus läuft auf **9090**, Grafana auf **3000**  
- Fertiges Monitoring-Dashboard ist sofort nutzbar

## 5. Borkbackup installieren
### Nun muss noch die Backupsoftware installiert werden
### Link zum [Installationsguide](https://github.com/Lucsifer/LF10b-Project/blob/main/Backupserver-config-guide.md#create-remote-repository)
### Kurzbeschreibung:  
Der Guide zeigt, wie ein Remote-BorgBackup-Repository über SSH eingerichtet wird.  
Dabei wird ein Backup-Benutzer genutzt, das Repository initialisiert und der Schlüssel exportiert, damit später automatisierte Backups möglich sind.

### Ergebnis:  
- Remote Borg-Repository ist eingerichtet  
- Schlüssel ist außerhalb des Backupservers gesichert  
- System ist bereit für automatisierte Backups

## 6. Backup wieder einspielen
### Backups auflisten
```bash
borg list backupuser@192.168.65.169:/BACKUP
```
### Richtiges Backup restoren
```bash
cd /
sudo borg extract --progress --verbose backupuser@192.168.65.169:/BACKUP::test001 /
```
