# 5. Risikoanalyse

## 5.1 Ziel und Umfang der Risikoanalyse

Ziel der Risikoanalyse ist die systematische Identifikation, Bewertung und Behandlung von Risiken für die Pterodactyl Minecraft Server Infrastruktur.
Die Risikoanalyse baut auf der zuvor durchgeführten Schutzbedarfsfeststellung auf und orientiert sich an den Vorgaben des Bundesamtes für Sicherheit in der Informationstechnik (BSI) gemäß BSI-Standard 200-2.

Der Analyseumfang umfasst folgende Bestandteile:

- Pterodactyl Panel und Wings
- Docker Infrastruktur
- Ansible Control Host
- Backup-Server (Borg)
- Monitoring-System (Prometheus und Grafana)
- Virtualisierungsumgebung (VMware Fusion)
- Host-Systeme (MacBooks)

---

## 5.2 Identifikation schutzwürdiger Assets

Im Rahmen der Risikoanalyse wurden folgende schutzwürdige Assets identifiziert:

| Asset | Beschreibung |
|--------|--------------|
| SSH-Keys | Authentifizierung für administrative Zugriffe auf Server |
| API-Keys (Pterodactyl) | Steuerung und Verwaltung der Container |
| Backups | Wiederherstellung der Minecraft-Welten und Konfigurationen |
| Ansible Playbooks | Automatisierte Systemkonfiguration |
| Docker Host | Ausführung aller Container |
| Panel-Datenbank | Benutzer- und Konfigurationsdaten |

---

## 5.3 Identifizierte Risiken und Bewertung

### Risiko R01 – Kompromittierung eines SSH-Keys

**Beschreibung:**  
Ein Angreifer erlangt Zugriff auf einen privaten SSH-Schlüssel eines Administrators.

**Betroffene Schutzziele:**  
Vertraulichkeit, Integrität, Verfügbarkeit

**Eintrittswahrscheinlichkeit:** mittel  
**Schadensausmaß:** hoch  
**Risikobewertung:** hoch  

**Bestehende Maßnahmen:**
- SSH ausschließlich mit Key-Authentifizierung
- Deaktivierter Root-Login
- Firewall-Regeln zur Zugriffsbeschränkung

**Zusätzliche Maßnahmen:**
- Regelmäßige Key-Rotation
- Einsatz von Fail2Ban
- Getrennte Administrator-Accounts
- Optional Zwei-Faktor-Authentifizierung für Panel-Zugriffe

**Restrisiko:** mittel bis gering

---

### Risiko R02 – Manipulation oder Verlust von Backups

**Beschreibung:**  
Backups werden manipuliert, gelöscht oder verschlüsselt, sodass eine Wiederherstellung nicht möglich ist.

**Betroffenes Schutzziel:**  
Integrität

**Eintrittswahrscheinlichkeit:** gering bis mittel  
**Schadensausmaß:** hoch  
**Risikobewertung:** mittel  

**Bestehende Maßnahmen:**
- Verschlüsselte Borg-Repositories
- Automatisierte Backup-Prozesse

**Zusätzliche Maßnahmen:**
- Regelmäßige Integritätsprüfung der Backups
- Durchführung von Test-Wiederherstellungen
- Write-Only-Zugriff für Backup-Prozesse

**Restrisiko:** gering

---

### Risiko R03 – Docker Container Escape

**Beschreibung:**  
Ein kompromittierter Minecraft-Container bricht aus der Isolation aus und erhält Zugriff auf das Host-System.

**Betroffene Schutzziele:**  
Vertraulichkeit, Integrität, Verfügbarkeit

**Eintrittswahrscheinlichkeit:** gering  
**Schadensausmaß:** hoch  
**Risikobewertung:** mittel  

**Bestehende Maßnahmen:**
- Nutzung nicht-privilegierter Container
- Trennung von Diensten
- Regelmäßige Systemupdates

**Zusätzliche Maßnahmen:**
- Einsatz von User-Namespaces
- Härtung der Docker-Konfiguration (z. B. Read-only Filesystem)
- Minimale Container-Images

**Restrisiko:** gering

---

### Risiko R04 – DDoS-Angriff auf den Minecraft-Port

**Beschreibung:**  
Ein Distributed-Denial-of-Service-Angriff führt zu einer Überlastung des öffentlich erreichbaren Ports.

**Betroffenes Schutzziel:**  
Verfügbarkeit

**Eintrittswahrscheinlichkeit:** mittel  
**Schadensausmaß:** normal  
**Risikobewertung:** mittel  

**Bestehende Maßnahmen:**
- Firewall-Konfiguration
- Monitoring der Netzwerkauslastung

**Zusätzliche Maßnahmen:**
- Rate-Limiting
- Reverse Proxy oder vorgeschalteter Schutzmechanismus

**Restrisiko:** normal

---

### Risiko R05 – Fehlkonfiguration durch Administrator

**Beschreibung:**  
Durch menschliches Versagen werden fehlerhafte Konfigurationen oder Playbooks ausgerollt.

**Betroffenes Schutzziel:**  
Integrität

**Eintrittswahrscheinlichkeit:** hoch  
**Schadensausmaß:** normal bis hoch  
**Risikobewertung:** hoch  

**Bestehende Maßnahmen:**
- Nutzung einer Testumgebung
- Versionskontrolle der Playbooks

**Zusätzliche Maßnahmen:**
- Code-Review-Prinzip
- Dokumentierte Freigabeprozesse
- Rollback-Strategie

**Restrisiko:** mittel

---

## 5.4 Risikoregister

| Nr. | Asset | Bedrohung | Schutzziel | Eintrittswahrscheinlichkeit | Schadensausmaß | Risiko | Bestehende Maßnahmen | Zusätzliche Maßnahmen | Restrisiko |
|------|--------|------------|-------------|----------------------------|----------------|--------|---------------------|----------------------|------------|
| R01 | SSH-Keys | Kompromittierung | V, I, V | mittel | hoch | hoch | Key-Authentifizierung | 2FA, Key-Rotation | mittel |
| R02 | Backups | Manipulation | Integrität | gering-mittel | hoch | mittel | Verschlüsselung | Test-Restore | gering |
| R03 | Docker Host | Container Escape | V, I, V | gering | hoch | mittel | Nicht-privilegierte Container | Härtung | gering |
| R04 | Minecraft-Port | DDoS | Verfügbarkeit | mittel | normal | mittel | Firewall | Rate-Limiting | normal |
| R05 | Ansible | Fehlkonfiguration | Integrität | hoch | normal-hoch | hoch | Testsystem | Code-Review | mittel |

---

## 5.5 Fazit der Risikoanalyse

Die Risikoanalyse zeigt, dass trotz insgesamt normalem Schutzbedarf einzelne technische Komponenten ein erhöhtes Risiko aufweisen können.  
Insbesondere administrative Zugänge, Backup-Integrität und Konfigurationsprozesse stellen relevante Risikofaktoren dar.  

Durch geeignete technische und organisatorische Maßnahmen können die identifizierten Risiken jedoch auf ein akzeptables Restrisiko reduziert werden.