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

## 2. Schritt SSH für Ansible 

### Prüfen ob SSH bereits mit installiert wurde
```bash
sudo systemctl status ssh
```
Wenn "not found", dann muss SSH nachinstalliert werden

### SSH instalieren
```bash
sudo apt install openssh-server
```
## 3. Schritt Ansible-Skript für die Einrichtung

## 
