# VM config
6144MB RAM
64GB SSD
4 Cores

# Ubuntu Server Installation
```json
Setup Configuration {
    "Language": "English",
    "Update Installer": True,
    "Keyboard" : "German - German",
    "Install-Type": "Normal",
    "Search 3rd party driver": True,
    "Disk as LVM group" : False,
    "User" : {
        "Full Name" : "LF10backup",
        "Servername" : "backup",
        "username" : "lf10backup",
        "password" : "lf10backup"
    },
    "Ubuntu Pro" : False,
    "OpenSSH Server" : True
}
```

## System Update
nach reboot ausführen:
`sudo apt update && sudo apt upgrade -y`

# Backup Solution
install borg backup
`sudo apt install borgbackup -y`

## Prepare user
`sudo useradd -m backupuser`
-m erstellt ein Home verzeichniss. Weil Linux das Warum auch immer standardmäßig keins erstellt
`sudo passwd backupuser`
Set new password (backupuser)

## Prepare dir for backups
```shell
sudo mkdir /BACKUP
sudo chown backupuser:backupuser /BACKUP #backupuser is the user that is the owner of the backup files
```

## Create Remote Repository
! On the main server !

```shell
# Install Borg
sudo apt install borgbackup -y

# Init remote repo via ssh
borg init backupuser@192.168.65.169:/BACKUP
#Borgrepo PW = backupuser

# export key
borg key export backupuser@192.168.65.169:/BACKUP > /home/lf10b/backupkey.txt #Stored not on the backupserver

# Create backup to remote repo
sudo borg create -s --progress --verbose backupuser@192.168.65.169:/BACKUP::test001 /home /var/log /usr/share /etc /var/www /usr/local /var/lib
```

To list all backups:
`borg list backupuser@192.168.65.169:/BACKUP`

## Restore backup
