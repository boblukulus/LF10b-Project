# Wiederanlaufplan Projekt-Pterodactyl
<img width="1240" height="954" alt="image" src="https://github.com/user-attachments/assets/8e9cf52d-da08-4e63-8056-5cdddea55b00" />


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

### Installiere relevanter Dienste
- BorgBackup ``` sudo apt install borgbackup ```
- Ansible ``` sudo apt install ansible ```


## 2. Schritt Ansible-Skript für die Einrichtung

- Ansible-Skript aus github ziehen
- IP adressen anpassen in `inventory`, `ansible.cfg`, `group_vars/all.yml`
- Playbook ausführen: `ansible-playbook main.yml --ask-become-pass --ask-vault-pass`
- Letzte Installationsschritte ausführen -> [Pterodactyl Doku](https://pterodactyl.io/wings/1.0/installing.html#configure) (Kann leider aus technischen Gründen nicht automatisiert werden.)

## 3. Schritt EInrichten / installieren von Monitoring
Es muss nun Graphana und Prometheus installiert werden
Link zum [Installationsguide](https://github.com/Lucsifer/LF10b-Project/blob/main/Server-config-guide.md#monitoring-23-sept-2025)

### Kurzbeschreibung: 
Der Guide erklärt, wie Node Exporter, Minecraft Exporter, Prometheus und Grafana installiert und verbunden werden.

### Ergebnis:  
- Metriken vom Server & Minecraft werden gesammelt  
- Prometheus läuft auf **9090**, Grafana auf **3000**  
- Fertiges Monitoring-Dashboard ist sofort nutzbar


## 5. Backup wieder einspielen
### Backups auflisten
```bash
borg list backupuser@192.168.65.169:/BACKUP
```
### Richtiges Backup restoren
```bash
cd /
sudo borg extract --progress --verbose backupuser@192.168.65.169:/BACKUP::test001 /
```
