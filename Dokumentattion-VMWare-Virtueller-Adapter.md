 # Virtueller Adapter in VM Ware
## Hintergrund
Da wir zu dritt sind und jeder von uns auf seinem eigenen Rechner arbeitet, müssen wir ein Bridged Virtual Network für die VMs verwenden.
Durch die Bridged-Einstellung werden die virtuellen Maschinen wie eigenständige Geräte im gleichen Netzwerksegment wie der jeweilige lokale Host eingebunden. Jede VM erhält dadurch eine eigene IP-Adresse vom DHCP-Server (z. B. vom Router).

Dies hat den Vorteil, dass die VMs nicht nur mit ihrem Host, sondern auch untereinander und mit anderen Geräten im Netzwerk direkt kommunizieren können. Auf diese Weise lassen sich z. B. Serverdienste, gemeinsame Testszenarien oder verteilte Anwendungen problemlos realisieren, als ob die VMs physische Rechner im selben LAN wären.

## Umsetzung
Unter VMware Workestation kann man den bridged Netzwerkadapter entweder während der installation oder später in den "Settings" der VM einrichten.

### Während der installation

Beim durchführen der installation sollte man direkt beim start des "New Virtaul Maschine Wizards" die Option <img width="97" height="19" alt="image" src="https://github.com/user-attachments/assets/95bb7b1e-3d56-4102-99fd-6acbe6dfbca1" /> auswählen.

Während der installation gibt es dann direkt einen Teilschritt der wiefolgt aussieht:

<img width="322" height="313" alt="image" src="https://github.com/user-attachments/assets/fe4349ed-379c-4290-94fe-11fe0ac318e3" />

hier den Bridged Adapter auswählen und mit der Einrichtung fortfahren.
!! Es ist möglich das im Nachgang trotzdem noch einstellungen getroffen werden müssen siehe "Adapter anpassen"

### Nachträglich abändern

In VMWare bei der ausgewählten VM "strg + D" drücken um die Settings der VM zu öffnen, dies geht alternativ auch über die Menü-Leitse oben unter VM.
Dort kann man nun unter "Networke Adapter" auswählen, was für ein Virtuelles Netzwerk genutzt werden soll.
Im Optimalfall kennt man den Namen des Adapters, der ein bridged Virtuelnetworke zur verfügung stellt: im Beispiel "VMnet0", andernfalls oben über den Reiter "Bridged"

<img width="555" height="533" alt="image" src="https://github.com/user-attachments/assets/589fe82b-d1db-416a-be23-b01f96f730a1" />

### Adapter anpassen

Damit der bridged Adapter nun auch das gleiche Netzwerk nutzt, wie der local host muss noch folgendes geprüft werden:
1. in den Virtual Networke Editor navigieren
2. das entsprechende virtuelle Netzwerk auswählen (Bsp.: VMnet0)
3. dort nachprüfen das Bridged ausgewählt ist
4. in der dropbox "Bridged to:", den entsprechenden Adapter auswählen. (Bsp.: Wlan Adapter)

<img width="447" height="388" alt="image" src="https://github.com/user-attachments/assets/9c6cf577-ed16-4425-b0b1-21279f0c6c0c" />

