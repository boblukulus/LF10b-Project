# Pterodactyl - Minecraft Server

## Inhaltsverzeichnis

1. Die Projektgruppe
	1. Informationstechnik
2. Sicherheitsmanagement
	1. Organisationsstruktur und Verantwortlichkeiten
3. Strukturanalyse
	1. Erfassung der Anwendungen
	2. Erhebung des Netzplans
	3. Erhebung der IT-Systeme
4. Schutzbedarfsfeststellung
	1. Anpassung der Schutzbedarfskategorien
	2. Schutzbedarfsfeststellung für Anwendungen
	3. Schutzbedarfsfeststellung für Kommunikationsverbindungen
5. Eigenmotivation
	1. Was habe ich gelernt?
	2. Wie kommt man zu dem Ergebnis?
	3. Wo findet man hilfreiche Informationen zu dem Thema?


## 1.  Die Projektgruppe
In diesem Dokument wird der erste größere Versuch unternommen das bereits durchgeführte Projekt (Pterodactyl-Server) nach der Variante "Basis-Absicherung" abzusichern und praktisch zu lernen, was es bedeutet mittels des Grundschutzkompendium das erschaffene System soweit abzusichern, wie unser kollektiver Wissenstand es ermöglicht.

Die Projektgruppe besteht weiterhin aus: Lucas, Robert und Luis

### 1.1 Informationstechnik
Es werden zwei MacBooks verwendet für die Verwaltung von Virtual Machines für das Testen und Vorführen des Projektes. Beide sind auf der neuesten macOS Version "macOS Tahoe 26.1" (Stand 04.12.2025). Alle Virtuellen Maschinen benutzen das Betriebsystem Ubuntu Version 24.04.3 LTS. Die Virtual Machines werden für folgende Zwecke eingesetzt:
- Ansible-Control-Host, der die Automatisierungs- und Konfigurationsverwaltung über Ansible ausführt,
- ein Produktivsystem für das Bereitstellen des Webinterfaces und die Verwaltungslogik für den Gameserver, 
- einen Backup-Server der automatisiert die Minecraft-Welten der Spieler sichert sowie andere relevante Daten,
- und einen Daemon-Server, der die Docker Container betreibt und einen Zugang für diese bereitstellt, sodass sich Nutzer nur auf den Docker Container verbinden können/müssen.

## 2. Sicherheitsmanagement
Umfassende Informationen zu den behandelten Themen und weitere Aspekten, die beim Aufbau eines Informationssicherheitsmanagementsystems zu berücksichtigen sind, sind in folgenden Dokumenten aufzufinden:
- BSI-Standard 200-1: Managementsysteme für Informationssicherheit (ISMS)
- BSI-Standard 200-2: IT-Grundschutz-Methodik
- IT-Grundschutz-Kompendium, Baustein ISMS.1 Sicherheitsmanagement und die zugehörigen Umsetzungshinweise

### 2.1 Projektstruktur und Verantwortlichkeiten
Unsere Gruppe (Robert, Lucas, Luis) beabsichtigt, ein Sicherheitskonzept auszuarbeiten, das für das Projekt umgesetzt werden soll um die Basis-Absicherung mindestens gewährleisten zu können, aufgrund unseres bisherigen Wissensstandes.

Im ersten Schritt haben wir Luis zum Informationssicherheitsbeauftragten ernannt, da er sich mit der Thematik IT-Grundschutz für das Projekt am meisten auseinandersetzt und die Fähigkeit hat das erlernte Wissen an seine Projektgruppe sinnvoll und in einem ausreichenden Maße weiterzugeben. 

##### Anmerkung
Da kein Unternehmen, sondern nur eine kleine Projektgruppe hinter dem Lernprojekt steht müssen keine Geschäftsprozesse, Räumlichkeiten, Abteilungen, etc. beschrieben und untersucht werden.

## 3. Strukturanalyse
Grundlage eines jeden Sicherheitskonzeptes ist eine genaue Kenntnis der im betrachteten Informationsverbund vorhandenen Informationen, ihres Stellenwertes für die Durchführung für Anwendungen, sowie der technische Rahmenbedingungen, in denen sie verwendet und verarbeitet werden, bezogen auf das Gameserver Projekt. Bei der Strukturanalyse geht es darum, die dazu erforderlichen Informationen zusammenzustellen und so aufzubereiten, dass sie die weiteren Schritte der IT-Grundschutz-Methodik unterstützen.

Zu der auf unser Projekt angepassten Strukturanalyse gehören folgende Teilschritte:
1. Netzplanerhebung,
2. Erfassung der IT-Systeme sowie
3. Erfassung der Anwendungen

### 3.1 Netzplanerhebung
![[Pasted image 20251205123934.png]]
### 3.2 Erfassung der IT-Systeme
In den Tabellen sind die IT-Systeme jeweils durchnummeriert.
Der Vorangestellte Buchstabe kennzeichnet jeweils ihren Typ (L = Laptop, VS = Virtual Server (als VM), H = Hypervisor)

| Nr   | Beschreibung                                 | Plattform                         | Standort  | Anzahl |
| ---- | -------------------------------------------- | --------------------------------- | --------- | ------ |
| L01  | Macbook Pro (Produktivsystem bereitstellung) | MacOS 26.1 Tahoe                  | Physisch  | 1      |
| L02  | Macbook Air (Automation Testsystem)          | MacOS 26.1 Tahoe                  | Physisch  | 1      |
| H01  | VMware Fusion Hypervisor                     | VMware Fusion Professional 13.6.4 | L01 & L02 | 2      |
| VS01 | Pterodactyl Server (Produktivsystem)         | Ubuntu Server 24.04.3 LTS aarch64 | L01       | 1      |
| VS02 | Borg Backup Server (Produktivsystem)         | Ubuntu Server 24.04.3 LTS aarch64 | L01       | 1      |
| VS03 | Ansible Control Host (Produktivsystem)       | Ubuntu Server 24.04.3 LTS aarch64 | L01       | 1      |
| VS04 | Pterodactyl Server (Testsystem)              | Ubuntu Server 24.04.3 LTS aarch64 | L02       | 1      |
| VS05 | Borg Backup Server (Testsystem)              | Ubuntu Server 24.04.3 LTS aarch64 | L02       | 1      |
| VS06 | Ansible Control Host (Testsystem)            | Ubuntu Server 24.04.3 LTS aarch64 | L02       | 1      |

### 3.3 Erfassung der Anwendungen
| Bezeichnung | Name und Beschreibung der Anwendung                                                                                         | Anzahl | Benutzer | Verantwortlich/Admin        |
| ----------- | --------------------------------------------------------------------------------------------------------------------------- | ------ | -------- | --------------------------- |
| A001        | **Docker:**<br>Plattform zum Erstellen und Ausführen isolierter Container für Anwendungen.                                  | 10     | 13       | Admin<br>(Luis)             |
| A002        | **Pterodactyl:**<br>Web-basiertes Panel zum Verwalten von Game- und App-Servern in Docker-Containern.                       | 10     | 11       | Admin<br>(Luis)             |
| A003        | **Grafana:**<br>Dashboard Tool zum Visualisieren von Metriken, Logs, Systemdaten:                                           | 1      | 3        | Monitoring Team<br>(Robert) |
| A004        | **Prometheus:**<br>Monitoring- und Metriksystem, das Daten sammelt und für Alerts und Grafana bereitstellt:                 | 1      | 3        | Monitoring Team<br>(Robert) |
| A005        | **Borg:**<br>Deduplizierendes, verschlüsselbares Backup-Tool für effiziente und sichere Datensicherung                      | 1      | 3        | Backup Team<br>(Lucas)      |
| A006        | **Ansible:**<br>Automatisierungswerkzeug zur Orchestrierung und allgemeinen Konfiguration und Administration von Computern. | 1      | 1        | Admin<br>(Luis)             |



## 4. Schutzbedarfsfeststellung
Zur Schutzbedarfsfeststellung gehören die folgenden Aktivitäten:
1. die auf eine Institution zugeschnittene Definition von Schutzbedarfskategorien (z. B. "normal", "hoch", "sehr hoch"),
2. die Feststellung des Schutzbedarfs der in der Strukturanalyse erfassten Anwendungen mit Hilfe dieser Kategorien,
3. die Ableitung des Schutzbedarfs der IT-Systeme aus dem Schutzbedarf der Anwendungen,
4. daraus abgeleitet die Feststellung des Schutzbedarfs der Kommunikationsverbindungen sowie
5. die Dokumentation aus Auswertung der vorgenommenen Einschätzungen.

-> Weitere Infos zur Schutzbedarfsfeststellung sind aufzufinden im BSI-Standard 200-2, Kapitel 8.2.

### 4.1 Anpassung der Schutzbedarfskategorien

#### Normaler Schutzbedarf:
Gilt wenn eine Beeinträchtigung überschaubar, tolerierbar oder mit einfachen Mitteln ausgleichbar ist.

##### Vertraulichkeit - Normal
- Inhalte des Pterodactyl-Servers sind nicht hochsensibel (z.B. Game-Server, öffentl. Dienste)
- Angreifer könnten die Infrastruktur analysieren, aber es entsteht kein kritischer Datenverlust
##### Integrität - Normal
- Kleinere Manipulationen im Monitoring sind nicht kritisch
- Falsche Prometheus-Daten führen zu Fehlinterpretationen, aber nicht zu erheblichen Schäden
##### Verfügbarkeit - Normal
- Kurze Ausfälle sind akzeptabel
- Nutzer sind für das Spielen von Minecraft vom Server abhängig, aber es kommt kein Geschäft zustande, da die Server Nutzung kein Geld kostet

#### Hoher Schutzbedarf (falls das Projekt online wäre)
Gilt wenn ein Ausfall erhebliche Schäden verursacht, z.B. Datenverlust, längerfristiger Ausfall, Reputationsschäden oder wirtschaftliche Nachteile.

##### Vertraulichkeit - Hoch
- Ansible-SSH-Keys, API-Tokens, Vault-Passwörter -> sind alle kritisch für Authentifizierung
- Zugriff auf Backups durch Fremde, würde die Datenwiederherstellung gefährden
- Zugangsdaten des Pterodactyl-Admin-Panel würden geleakt werden
##### Integrität - Hoch
- Manipulierte Ansible Playbooks könnten Server komprimittieren
- Manipulierte Backups würden erhebliche Auswirkungen auf Wiederherstellung haben
- Falsche Prometheus-Daten könnten Angriffe verschleiern
##### Verfügbarkeit - Hoch
- Ausfall des Backup-Servers -> Wiederanlauf wird schwierig
- Ausfall des Control Host -> automatisiertes Deployment nicht möglich
- Ausfall des Pterodactyl-Servers -> gesamter Betrieb des Gameservers betroffen

#### Sehr hoher Schutzbedarf
Gilt wenn ein Ausfall katastrophale oder existenzbedrohende Schäden verursacht – normalerweise bei Medizin, Energie, Behörden, Forschung, Unternehmen, personenbezogene Daten hoher Sensitivität.

In unserem Projekt gibt es keinen sehr hohen Schutzbedarf im realen Sinne, da keine katastrophalen oder existenzbedrohenden Schäden verursacht werden.

## 4.2 Schutzbedarfsfeststellung für Anwendungen
| Nr.  | Bezeichnung                    | Grundwert       | Schutzbedarf | Begründung                                                                                                                                                                                                                                                                                                                                                                        |
| ---- | ------------------------------ | --------------- | ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| A001 | Docker                         | Vertraulichkeit | normal       | Die Docker Container enthalten keine sensiblen Daten. Container Images, Konfigurationen oder Logs enthalten keine personenbezogenen oder geheimen Daten. Somit würden Angriffe keine kritischen Daten offenlegen.                                                                                                                                                                 |
|      |                                | Integrität      | normal       | Die Integrität des Containers würde sich auf die Minecraft Welt beziehen. Eine Manipulation der Spielwelt ist für das Projekt und den Nutzer nicht kritisch.                                                                                                                                                                                                                      |
|      |                                | Verfügbarkeit   | normal       | Die Verfügbarkeit des Docker Containers betrifft den laufenden Minecraft Server. Da kein Geschäft mit dem Server gemacht wird entsteht kein finanzieller Schaden, sondern eher ein verkraftbarer Reputationsschaden, da der Server immerhin kostenlos bereitgestellt wird.                                                                                                        |
| A002 | Pterodactyl<br>(Panel & Wings) | Vertraulichkeit | normal       | Das Pterodactyl-Panel enthält administrative Daten, wie Benutzerkonten, Serverkonfigurationen, API-Keys und Zugangsdaten für Docker / Wings. Diese Daten sind intern, aber nicht hochsensibel. Bei Änderungen der Serverkonfigurationen entstehen trotzdem keine wirtschaftlichen Schäden.                                                                                        |
|      |                                | Integrität      | normal       | Integrität betrifft Konfigurationen, Server-Einstellungen, Admin-Daten, Startparameter und Benutzerrollen im Panel. Die Manipulation von Pterodactyl-Daten kann zur fehlerhaften Konfiguration der Server führen (z.B. unerwünschte Ports, falsche Ressourcenlimits). Auswirkungen sind zwar technisch ärgerlich, aber nicht kritisch.                                            |
|      |                                | Verfügbarkeit   | normal       | Die Verfügbarkeit des Pterodactyl Panel beeinflusst Start/Stop von Servern, Verwaltung und Monitoring, Zugriff auf Konsolen und Verwaltung von Benutzern. Wenn das Pterodactyl ausfällt können Minecraft Server zeitweise nicht verwaltet werden, aber die meisten Container sollten weiterhin laufen. Da das System nicht geschäftskritisch ist, sind Ausfälle auch verkraftbar. |
| A003 | Grafana                        | Vertraulichkeit | normal       | Grafana Daten sind nicht sensibel und ein Angreifer bekommt nur Systemmetriken und Dashboard Konfigurationen, die keinen kritischen Schaden verursachen.                                                                                                                                                                                                                          |
|      |                                | Integrität      | normal       | Die Manipulation des Dashboards wäre störend, aber nicht kritisch für den Betrieb. Die Wiederherstellung würde durch ein Backup schnell gemacht sein.                                                                                                                                                                                                                             |
|      |                                | Verfügbarkeit   | normal       | Wenn Grafana ausfällt, ist nur die Anzeige der Daten gestört und Monitoring und Log-Systeme laufen weiter. So können Daten später wieder erhoben werden und es entstehen trotzdem keine Lücken.                                                                                                                                                                                   |
| A004 | Prometheus                     | Vertraulichkeit | normal       | Prometheus speichert technische Metriken und keine personenbezogenen Daten. Ein Angreifer könnte zwar die Systeminformationen auslesen, aber es würde dadurch kein Schaden direkt entstehen.                                                                                                                                                                                      |
|      |                                | Integrität      | normal       | Durch die Manipulation von Metriken könnten falsche Auswertungen erzeugt werden, die aber den Betrieb nicht stören würde. Im Notfall lässt sich Prometheus einfach neu deployen.                                                                                                                                                                                                  |
|      |                                | Verfügbarkeit   | normal       | Wenn Grafana ausfällt ist nur die Anzeige der Daten gestört. Das Monitoring & Systeme laufen weiter.                                                                                                                                                                                                                                                                              |
| A005 | Borg-Backup                    | Vertraulichkeit | normal       | Die Backups enthalten Minecraft Welten und Konfigurationen die somit also nichts hochsensibles enthalten. Der Verlust der Daten wäre störend/nervig, aber es entsteht kein kritischer Schaden. Es werden zudem keine personenbezogenen Daten gespeichert.                                                                                                                         |
|      |                                | Integrität      | normal       | Manipulierte Backups könnten eine Wiederherstellung verhindern. Da aber keine geschäftliche Abhängigkeit besteht gibt es keine hohe Kritikalität.                                                                                                                                                                                                                                 |
|      |                                | Verfügbarkeit   | normal       | Ausfall des Backupservers bedeutet Verlust von Wiederherstellungsoptionen. Da aber keine geschäftliche Abhängigkeit besteht gibt es keine hohe Kritikalität. Wichtige Daten können zur Not neu erzeugt werden.                                                                                                                                                                    |
| A006 | Ansible                        | Vertraulichkeit | normal       | Arbeitet mit SSH-Keys, Inventory-Datei und Zugangsdaten. Diese sind technisch relevant aber nicht kritisch, da keine Verträge oder ein Geschäft dahinter steht.                                                                                                                                                                                                                   |
|      |                                | Integrität      | normal       | Manipulierte Playbooks könnten fehlerhafte Installationen ausrollen, aber die Systeme sind jederzeit neu aufsetzbar. Damit würde in der Realität ein hoher Schutzbedarf angemessen sein, da aber kein Geschäft dahinter steht ist ein normaler Schutzbedarf angebrachter.                                                                                                         |
|      |                                | Verfügbarkeit   | normal       | Da das Ansible Playbook nur einmal durchlaufen muss und der anschließende Betrieb der Minecraft Server, von dem Prozess nicht abhängig ist und auch keinen geschäftlichen Mehrwert hat, bedarf es keinem hohen Schutzbedarf.                                                                                                                                                      |

## 4.3 Schutzbedarfsfeststellung für Kommunikationsverbindungen
| Bezeichnung                          | Grundwert       | Schutzbedarf   | Begründung                                                                                                                                           |
| ------------------------------------ | --------------- | -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| Pterodactyl Panel <-> Wings          | Integrität      | Hoch           | Manipulierte Kommandos könnten Container manipulieren oder zerstören.                                                                                |
|                                      | Vertraulichkeit | Hoch           | API-Key-Kommunikation. Bei Kompromittierung könnte ein Angreifer Server starten/stoppen, Dateien verändern oder löschen.                             |
|                                      | Verfügbarkeit   | Normal         | Ausfall führt nur dazu, dass Server nicht verwaltet werden können. Kein geschäftlicher Schaden.                                                      |
| Admin <-> Pterodactyl Panel          | Vertraulichkeit | Normal - Höher | Benutzerkonten + Passwörter. Kein hochsensibles Material, aber Schutz nötig.                                                                         |
|                                      | Integrität      | Normal         | Manipulation könnte Nutzerkonten oder Einstellungen verändern.                                                                                       |
|                                      | Verfügbarkeit   | Normal         | Nutzer können Panel nicht nutzen → störend, aber nicht kritisch.                                                                                     |
| Wings <-> Docker Container           | Vertraulichkeit | Normal         | Minecraft-Weltdaten + Konfigs → nicht sensibel.                                                                                                      |
|                                      | Integrität      | Normal         | Manipulation betrifft nur Spielwelt/Serverfiles.                                                                                                     |
|                                      | Verfügbarkeit   | Normal         | Container fällt aus → MC-Server offline. Kein schwerwiegender Schaden.                                                                               |
| Prometheus <-> Exporter              | Vertraulichkeit | Normal         | Metriken enthalten keine sensiblen Daten.                                                                                                            |
|                                      | Integrität      | Normal         | Falsche Metriken → Monitoring unbrauchbar.                                                                                                           |
|                                      | Verfügbarkeit   | Normal         | Fehlende Daten stören Monitoring, aber nicht den Betrieb.                                                                                            |
| Grafana <-> Prometheus               | Vertraulichkeit | Normal         | Nur Monitoring-Daten.                                                                                                                                |
|                                      | Integrität      | Normal         | Daten könnten verfälscht dargestellt werden.                                                                                                         |
|                                      | Verfügbarkeit   | Normal         | Grafana-Ausfall beeinträchtigt den Betrieb nicht.                                                                                                    |
| Ansible Control Host <-> Ziel Server | Vertraulichkeit | Normal         | SSH-Schlüssel müssen geschützt sein, aber keine Unternehmensgefahr.                                                                                  |
|                                      | Integrität      | Normal         | Manipulierte Playbooks können Systeme beschädigen → hoher technischer Einfluss.                                                                      |
|                                      | Verfügbarkeit   | Normal         | Automatisierung fällt aus → unangenehm, aber nicht kritisch.                                                                                         |
| Backup Server <-> Pterodactyl        | Vertraulichkeit | Normal         | Backups enthalten Konfigs, aber kein hochsensibles Material.                                                                                         |
|                                      | Integrität      | Normal         | Manipulierte Backups könnten Wiederherstellung unmöglich machen. Normalerweise hoch, aber da kein geschäftskritischer Service dahinter steht normal. |
|                                      | Verfügbarkeit   | Normal         | Ausfall bedeutet keine Backups → ärgerlich, aber nicht kritisch.                                                                                     |


## 5. Eigenmotivation

### 5.1 Was habe ich gelernt?
Ich habe gelernt, wie man eine Schutzbedarfsanalyse nicht nur auf einzelne Szenarien wie bisher anwendet, sondern auch auf ein Projekt oder Unternehmen. Zum Ausarbeiten dieser SOL habe ich dieses Quelle vom BSI genutzt: https://www.bsi.bund.de/SharedDocs/Downloads/DE/BSI/Grundschutz/Hilfsmittel/Recplast/Beschreibung_Recplast.pdf?__blob=publicationFile&v=1 und die Sätze an geeigneten Stellen in der Schutzbedarfsanalyse mittels ChatGPT 5.0 "geglättet" nachdem ich die Originaltexte erarbeitet hatte. Weiterhin war es lehrreich das Projekt so weit auseinanderzunehmen, um wichtige Sicherheitsaspekte beschreiben zu können. Weiterhin konnte ich für mich mitnehmen wie man die Schutzbedarfskategorien anpasst auf sein Projekt und sinnvoll beschreibt.
-Luis

Ich finde es wichtig zu wissen wie man ein IT-System korrekt absichert, um die Daten jedes Nutzenden zu schützen. Das Thema IT-Sec wird leider viel zu oft vernachlässigt, weswegen ich möglichst viel über IT-Sicherheit lernen möchte, um in Zukunft den Aspekt der Sicherheit bei meinen Arbeitgebenden Firmen oder im Privaten/Freundesumfeld umsetzen zu können.
Ich habe gelernt, dass es nicht gerade einfach ist ein solches System abzusichern und wie die Praktiken des BSI-Grundschutzes angewand werden sowie wie man den Schutzbedarf, das Risiko und die möglichen Bedrohungen feststellt.
-Robert

Mein wichtigstes Learning aus der Arbeit an der Schutzbedarfsanalyse war, dass ein IT-System nur sehr schwer annähernd sicherer gemacht werden kann. Dafür muss man auch  zuerst versteht, wie unterschiedlich die Risiken und potenziellen Probleme tatsächlich sind. Die Analyse hat mir gezeigt, dass nicht jede Information und nicht jeder Prozess gleich kritisch ist und dass genau diese Unterschiede entscheidend dafür sind, wo Schutzmaßnahmen am wirkungsvollsten angesetzt werden können. 
-Lucas

### 5.2 Wie kommt man zu dem Ergebnis
Im Vorfeld habe ich mir Gedanken gemacht, welche Absicherung für unser Projekt innerhalb der mir zur verfügbaren Zeit am besten umzusetzen war und als das feststand habe ich angefangen auf der Seite des BSI nach Informationen, Anleitungen und Beispielen zu suchen, da unser Lehrer uns auf diese Quelle aufmerksam gemacht hatte. Danach hieß es nur noch lesen, verstehen und anwenden.

### 5.3 Wo findet man hilfreiche Informationen zum Thema?
https://www.bsi.bund.de/DE/Themen/Unternehmen-und-Organisationen/Standards-und-Zertifizierung/IT-Grundschutz/Zertifizierte-Informationssicherheit/IT-Grundschutzschulung/Online-Kurs-IT-Grundschutz/Lektion_1_Einstieg/Lektion_1_02/Lektion_1_02_node.html [10.12.2025, 13:50]

https://www.bsi.bund.de/SharedDocs/Downloads/DE/BSI/Grundschutz/Hilfsmittel/Recplast/Beschreibung_Recplast.pdf?__blob=publicationFile&v=1 [10.12.2025, 13:51]
