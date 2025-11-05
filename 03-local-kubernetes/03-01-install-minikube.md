## Übung 1: Lokale Kubernetes Instanz (Minikube)

**Ziel:** Ein lauffähiges, lokales Kubernetes-Cluster aufsetzen und mit `kubectl` verbinden.

Als Erstes brauchen wir eine Entwicklungsumgebung. `minikube` ist ein fantastisches Werkzeug, das ein komplettes Kubernetes-Cluster in einer einzigen virtuellen Maschine oder einem Docker-Container auf eurem Laptop startet.

### 1.1 Minikube Cluster erstellen

Die Wahl des 'Treibers' (Driver) hängt von eurem Betriebssystem ab. Das ist die Virtualisierungs-Technologie, die Minikube nutzt.

- **Windows-Nutzer:** Verwendet bitte den `hyperv` Treiber (setzt voraus, dass Hyper-V in Windows aktiviert ist).
    
- **MacOS-Nutzer:** Verwendet einen passenden Virtualisierungs-Treiber, z.B. `virtualbox`, `vmwarefusion` oder `qemu` (besonders auf Apple Silicon).
    
- **Linux-Nutzer / Docker Desktop Nutzer:** Der `docker` Treiber ist eine gute und einfache Wahl, da ihr Docker bereits kennt.
    

Wir fixieren die Kubernetes-Version, um sicherzustellen, dass wir alle dieselbe Umgebung haben. Wählt den passenden Befehl für euer System (ihr braucht nur _einen_ davon auszuführen!):

```
# Für Windows (mit Hyper-V):
minikube start --driver=hyperv --kubernetes-version=v1.37.0
```

```
# Für MacOS (Beispiel mit VirtualBox):
minikube start --driver=virtualbox --kubernetes-version=v1.37.0
```

```
# Für Linux oder alle mit Docker:
minikube start --driver=docker --kubernetes-version=v1.37.0
```

### 1.2 Kubectl Konfiguration abrufen

`minikube start` sollte eure `kubectl` Konfigurationsdatei (normalerweise unter `~/.kube/config`) automatisch aktualisiert haben. `kubectl` ist das Kommandozeilen-Werkzeug, um mit einem Kubernetes-Cluster zu interagieren.

Überprüfen wir den Status:

```
minikube status
```

(Sollte 'Running' anzeigen)

Schauen wir uns die Konfiguration an, die `minikube` für uns erstellt hat:

```
kubectl config view
```

### 1.3 Mit kubectl verbinden

Testen wir die Verbindung, indem wir die `Nodes` (Knoten) unseres Clusters abfragen. In unserem Fall sollte das nur ein einziger `Node` sein.

```
kubectl get nodes
```

Die Ausgabe sollte euren `minikube` Node mit dem Status 'Ready' zeigen.

### 1.4 Alternative: `minikube kubectl`

Manchmal arbeitet man mit mehreren Kubernetes-Clustern gleichzeitig. Um Konflikte in der `~/.kube/config` zu vermeiden, bietet `minikube` einen eingebauten Befehl, der `kubectl` direkt gegen das `minikube`-Cluster ausführt, ohne die globale Konfiguration zu ändern.

Probiert es aus:

```
minikube kubectl -- get nodes
```

Das Ergebnis sollte identisch sein. Für den Rest des Kurses verwenden wir aber das globale `kubectl`.
