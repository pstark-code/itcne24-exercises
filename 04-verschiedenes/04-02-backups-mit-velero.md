# (Siehe Ende der Datei!) Backup / Restore mit Velero

Es gibt viele Daten in einem Kubernetes Cluster, für die wir ein Backup haben wollen. 

- Wie Applikationen deployt werden (Deployment, StatefulSet, etc.)
- Wie das Netzwerk konfiguriert ist (Ingress, Service, etc.)
- Zertifikatinformationen ("Cert-Manager" Certificates)
- Persistente Daten (PVC/PV)

Die ersten drei Punkte können wir grösstenteils abdecken indem wir unseren Benutzern ArgoCD oder etwas vergleichbares anbieten und nach GitOps Prinzipien nur Ressourcen auf dem Cluster erlauben, die schon in einem Git Repository committed sind. 

Persistente Daten (hingegen, das ist ein ganz klarer Fall für ein traditionelles Backup. Dafür gibt es viele Lösungen, und ich möchte euch heute Velero näher bringen. Das ist eine Kubernetes-native Backuplösung, die ~~üblicherweise~~ S3-Objektstores als Backup-Ziel anzielt~~, aber auch auf ein File-System speichern kann~~.

Links:

- [Velero Home Page](https://velero.io/)
- [Installation](https://velero.io/docs/v1.17/basic-install/)
- ~~[File-System Backup](https://velero.io/docs/v1.17/file-system-backup/)~~
- [Liste von Storage-Provider Plugins](https://velero.io/docs/v1.17/supported-providers/)

~~Wir werden heute das File-System Backup verwenden, damit wir nicht zusätzlich noch einen S3 Server deployen müssen.~~



### Update:

Es stellt sich heraus, dass ich die Velero-Dokumentation an einem kritischen Punkt falsch verstanden hatte und die Software "Velero" ausschliesslich S3 als storage backend unterstützt. Mir fehlt jetzt die Zeit, die Übung zu erweitern um einen S3-Server deployen und konfigurieren. Ich werde die Übung trotzdem für alle interessierten hochladen, werde sie aber entsprechend als unfertig markieren.
 
Ich werde bis Mittwoch eine Lösung finden. Vielleicht werde ich die Übung aber auf dem Schul-Kubernetes aufbauen, damit ihr nicht so viel Infrastruktur rundherum braucht.

### Update 2: 

Für Mutige, hier ist ein von mir komplett ungetestetes Tutorial um Velero test-weise aufzusetzen mit einem Minio-Server als S3-kompatiblen Object Store:
https://velero.io/docs/v1.17/contributions/minio/