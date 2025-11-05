## Übung 2: Persistenter Speicher (Persistent Volumes)

**Ziel:** Verstehen, wie Daten in Kubernetes über den Lebenszyklus eines `Pods` hinaus gespeichert werden.

`Pods` sind flüchtig (ephemeral). Wenn ein `Pod` abstürzt oder neu gestartet wird, sind alle Daten darin verloren. Für Datenbanken oder andere stateful Applikationen brauchen wir persistenten Speicher.

### 2.1 Einen `Namespace` erstellen

Für jede Übung verwenden wir einen eigenen `Namespace`, um Ordnung zu halten. `Namespaces` sind virtuelle Cluster innerhalb eures physischen Clusters.

```
kubectl create namespace storage-uebung
```

Setzen wir diesen `Namespace` als unseren Standard für die nächsten Befehle:

```
kubectl config set-context --current --namespace=storage-uebung
```

### 2.2 Applikation (ohne Persistenz) deployen

Erstellen wir eine Datei `writer-deployment.yaml`. Diese App schreibt einfach das aktuelle Datum in eine Logdatei. **Wichtig:** Aktuell speichern wir die Logdatei _innerhalb_ des `Pod`-Containers, wo sie nicht persistent ist.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: writer-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: writer
  template:
    metadata:
      labels:
        app: writer
    spec:
      containers:
      - name: writer
        image: busybox
        # Dieser Befehl erstellt die Datei und schreibt alle 5 Sekunden hinein
        command: ["/bin/sh", "-c"]
        args:
          - >
            touch /data/log.txt;
            while true; do
              echo $(date) >> /data/log.txt;
              echo "--- Log-Inhalt ---";
              cat /data/log.txt;
              sleep 5;
            done
```

Deployen wir die App:

```
kubectl apply -f writer-deployment.yaml
```

Sucht den `Pod`-Namen und schaut euch die Logs an:

```
POD_NAME=$(kubectl get pods -l app=writer -o jsonpath='{.items[0].metadata.name}')
kubectl logs $POD_NAME -f
```

Ihr solltet sehen, wie die Logdatei wächst.

**Testen der Flüchtigkeit:** Löscht nun den `Pod` manuell. Das `Deployment` wird sofort einen neuen erstellen.

```
kubectl delete pod $POD_NAME
```

Wartet kurz und holt euch den Namen des _neuen_ `Pods` und schaut wieder in die Logs:

```
NEW_POD_NAME=$(kubectl get pods -l app=writer -o jsonpath='{.items[0].metadata.name}')
kubectl logs $NEW_POD_NAME -f
```

**Ergebnis:** Die Logdatei ist leer! Sie fängt wieder von vorne an. Die alten Daten sind weg.

### 2.3 `PersistentVolume` (PV) und `PersistentVolumeClaim` (PVC) erklärt

- **`PersistentVolume` (PV):** Stellt euch das als den "physischen" Speicher vor (z.B. eine Festplatte, ein Netzwerkspeicher). Ein PV ist eine Ressource im Cluster, genau wie ein `Node`. Es wird oft vom Cluster-Admin bereitgestellt.
    
- **`PersistentVolumeClaim` (PVC):** Das ist eine "Anfrage" (Claim) nach Speicher von einem Benutzer oder einer App. Die App sagt: "Ich brauche 1GB Speicher mit Lese/Schreib-Zugriff".
    
- **Beziehung:** Kubernetes "bindet" (binds) ein PVC an ein passendes, verfügbares PV. Der `Pod` mountet dann das PVC, nicht das PV direkt.
    
- **Docker-Vergleich:** Ein Docker Volume wird direkt deklariert. In K8s ist das entkoppelt: Der `Pod` (Entwickler) fordert Speicher an (PVC), und K8s (Admin/System) stellt ihn bereit (PV).
    
- **`StorageClass`:** Eine `StorageClass` (wie "standard", "fast-ssd", "backup") erlaubt die _dynamische_ Erstellung von PVs. Wenn ein PVC eine `StorageClass` anfordert, erstellt ein "Provisioner" im Hintergrund automatisch ein passendes PV. Minikube hat die `StorageClass` "standard" vorinstalliert.
    

### 2.4 PVC zum Deployment hinzufügen

Erstellen wir zuerst die "Anfrage" (PVC). Erstellt die Datei `data-pvc.yaml`:

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: data-pvc
spec:
  # "standard" ist die Standard-StorageClass in minikube
  storageClassName: standard
  accessModes:
    # ReadWriteOnce: Nur von einem Node gleichzeitig mountbar
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi # Wir fordern 1 GiB Speicher an
```

Anwenden:

```
kubectl apply -f data-pvc.yaml
```

Jetzt passen wir unser `writer-deployment.yaml` an, damit es dieses PVC nutzt. Wir fügen zwei Sektionen hinzu: `volumes` (auf `Deployment`-Ebene) und `volumeMounts` (auf `Container`-Ebene).

Erstellt die Datei `writer-deployment-pvc.yaml`:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: writer-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: writer
  template:
    metadata:
      labels:
        app: writer
    spec:
      containers:
      - name: writer
        image: busybox
        command: ["/bin/sh", "-c"]
        args:
          - >
            touch /data/log.txt;
            while true; do
              echo $(date) >> /data/log.txt;
              echo "--- Log-Inhalt ---";
              cat /data/log.txt;
              sleep 5;
            done
        # 1. Mountet das Volume im Container unter /data
        volumeMounts:
        - name: data-storage
          mountPath: /data
      # 2. Definiert das Volume und verknüpft es mit unserem PVC
      volumes:
      - name: data-storage
        persistentVolumeClaim:
          claimName: data-pvc
```

Aktualisieren wir das Deployment (die Datei `writer-deployment-pvc.yaml` _aktualisiert_ das bestehende `writer-app` Deployment):

```
kubectl apply -f writer-deployment-pvc.yaml
```

### 2.5 Storage Provisioner beobachten

Das `Deployment` erstellt einen neuen `Pod`, der nun das PVC mountet. Schauen wir uns an, was im Hintergrund passiert ist:

```
# Zeigt unser PVC, Status sollte "Bound" sein
kubectl get pvc
```

```
# Zeigt das automatisch erstellte PV!
kubectl get pv
```

Ihr seht jetzt ein `PersistentVolume`, das automatisch von der `StorageClass` "standard" erstellt und an unser `data-pvc` gebunden wurde.

### 2.6 Persistenz überprüfen

Lasst die App ein paar Sekunden laufen.

```
POD_NAME_PVC=$(kubectl get pods -l app=writer -o jsonpath='{.items[0].metadata.name}')
kubectl logs $POD_NAME_PVC -f
```

(Beendet mit Strg+C)

Jetzt löschen wir den `Pod` erneut:

```
kubectl delete pod $POD_NAME_PVC
```

Wartet auf den neuen `Pod` und prüft die Logs:

```
NEW_POD_NAME_PVC=$(kubectl get pods -l app=writer -o jsonpath='{.items[0].metadata.name}')
kubectl logs $NEW_POD_NAME_PVC -f
```

**Ergebnis:** Die alten Log-Einträge sind noch da! Die Daten sind jetzt persistent.

**Aufräumen:**

```
kubectl config set-context --current --namespace=default
kubectl delete namespace storage-uebung
```

(Das Löschen des `Namespace` löscht auch das `Deployment` und das `PVC`. Das `PV` wird danach automatisch freigegeben und gelöscht.)
