# Feste IP-Addresse trotz Cloud-Init
## Hintergrund
Beim bearbeiten des Netplans ist bei mir das Problem aufgetreten, das sich die ".yaml" Datei nach einem Reboot immer resetet hat.
Laut recherche liegt dies an Cloud-init, welches diese Datei beim reboot immmer wieder überschreibt.

## Umgehen von cloud-Init
Man kann Cloud-Init speziell für das Netzwerk deaktivieren indem man eine entsprechende Datei anlegt, dies geht wiefolgt:

### Netzwerk-Part von Cloud-Init blockieren:
```sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg```

### Inhalt der Datei:
```network: {config: disabled}```

## Netplan erstellen
Nun kann ein neuer Netplan erstellt werden, um die feste IP-Addresse zu vergeben:

### Eigenes Netplan-File anlegen/bearbeiten und speichern:
```sudo nano /etc/netplan/01-netcfg.yaml```

### Inhalt der Datei:

```yaml
network:
  version: 2
  
    renderer: networkd
    
    ethernets:
    
       ens33:
       
         dhcp4: no
         
         addresses:
         
          - 10.42.0.30/24
          
          routes:
          
          - to: default
          
            via: 10.42.0.1
            
          nameservers:
          
          addresses:
          
           - 8.8.8.8
           
           - 1.1.1.1
```

### Netplan anwenden

```sudo netplan apply```
