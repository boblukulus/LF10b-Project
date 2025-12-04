# VM config
3072MB RAM
32GB SSD
2 Cores

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
        "Full Name" : "ansible",
        "Servername" : "ansible",
        "username" : "ansible",
        "password" : "ansible"
    },
    "Ubuntu Pro" : False,
    "OpenSSH Server" : True
}
```

# After Install

```bash
sudo apt update && sudo apt upgrade -y
```

# Install Ansible

```bash
sudo apt install ansible git -y
```

