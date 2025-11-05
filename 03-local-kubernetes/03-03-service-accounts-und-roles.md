## Übung 3: Service Accounts, Roles und RoleBindings

**Ziel:** Berechtigungen für Prozesse _innerhalb_ des Clusters (z.B. CI/CD-Pipelines) verwalten.

Kubernetes nutzt Role-Based Access Control (RBAC). Es gibt zwei Arten von "Identitäten":

1. **`User`:** Für Menschen (wie uns). (Siehe Übung 4)
    
2. **`ServiceAccount` (SA):** Für Maschinen/Prozesse (z.B. ein `Pod`, der andere `Pods` deployen muss).
    

### 3.1 `Namespace` erstellen

```
kubectl create namespace rbac-uebung
kubectl config set-context --current --namespace=rbac-uebung
```

### 3.2 `ServiceAccount` erstellen

```
kubectl create serviceaccount deployer-sa
```

### 3.3 Token Secret für den SA erstellen

Früher (vor K8s 1.24) wurde automatisch ein `Secret` mit einem Token für jeden SA erstellt. Das ist heute aus Sicherheitsgründen nicht mehr so. Wir müssen das Token explizit anfordern. Der "alte" Weg, der oft in CI-Systemen noch genutzt wird, ist die Erstellung eines `Secret` vom Typ `kubernetes.io/service-account-token`.

Erstellt die Datei `sa-token-secret.yaml`:

```
apiVersion: v1
kind: Secret
metadata:
  name: deployer-sa-token
  # Wichtig: Diese Annotation verknüpft das Secret mit dem SA
  annotations:
    kubernetes.io/service-account.name: deployer-sa
type: kubernetes.io/service-account-token
```

Anwenden:

```
kubectl apply -f sa-token-secret.yaml
```

### 3.4 `kubectl` Konfig aus Token erstellen

Jetzt simulieren wir ein externes Programm (z.B. Jenkins), das sich mit diesem Token am Cluster anmelden will. Wir bauen eine separate `kubeconfig`-Datei.

```
# 1. Server-Adresse holen
SERVER=$(kubectl config view --minify -o jsonpath='{.clusters[0].cluster.server}')

# 2. CA-Zertifikat des Clusters aus dem Secret holen
CA_CERT_DATA=$(kubectl get secret deployer-sa-token -o jsonpath='{.data.ca\.crt}')

# 3. Das Token selbst holen und dekodieren
TOKEN=$(kubectl get secret deployer-sa-token -o jsonpath='{.data.token}' | base64 -d)

# 4. Neue kubeconfig-Datei 'sa-config.yaml' bauen
kubectl config set-cluster minikube-sa --server=$SERVER --certificate-authority=<(echo $CA_CERT_DATA | base64 -d) --embed-certs=true --kubeconfig=sa-config.yaml

kubectl config set-credentials deployer-sa --token=$TOKEN --kubeconfig=sa-config.yaml

kubectl config set-context deployer-context --cluster=minikube-sa --user=deployer-sa --namespace=rbac-uebung --kubeconfig=sa-config.yaml

kubectl config use-context deployer-context --kubeconfig=sa-config.yaml
```

Wir haben jetzt eine `sa-config.yaml`, die sich als `deployer-sa` authentifiziert.

### 3.5 `kubectl` Konfig testen (Fehlschlag erwartet)

Testen wir, ob unser `deployer-sa` `Pods` auflisten darf:

```
kubectl get pods --kubeconfig=sa-config.yaml
```

**Ergebnis:** `Error from server (Forbidden): pods is forbidden: User "system:serviceaccount:rbac-uebung:deployer-sa" cannot list resource "pods" in API group "" in the namespace "rbac-uebung"`

Das ist korrekt! Der `ServiceAccount` ist authentifiziert, aber nicht _autorisiert_. Er hat (noch) keine Rechte.

### 3.6 `Roles` und `RoleBindings` erklärt

- **`Role`:** Definiert Berechtigungen (Verben: 'get', 'list', 'create', 'delete'...) für Ressourcen ('pods', 'deployments'...). Eine `Role` ist **immer an einen `Namespace` gebunden**.
    
- **`RoleBinding`:** Verbindet ein "Subjekt" (z.B. `User`, `Group` oder `ServiceAccount`) mit einer `Role`.
    

### 3.7 `Role` und `RoleBinding` hinzufügen

Erstellen wir eine `Role`, die das Verwalten von `Deployments` und `Pods` erlaubt. Datei `deployer-role.yaml`:

```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: deployment-manager
rules:
# apiGroups "" (core) ist für Pods, "apps" ist für Deployments
- apiGroups: ["", "apps"]
  resources: ["pods", "deployments", "replicasets"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```

Anwenden:

```
kubectl apply -f deployer-role.yaml
```

Jetzt "binden" wir diese `Role` an unseren `deployer-sa`. Datei `deployer-rolebinding.yaml`:

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: deployer-binding
subjects:
# Das Subjekt ist unser ServiceAccount
- kind: ServiceAccount
  name: deployer-sa
  namespace: rbac-uebung
roleRef:
  # Die Rolle, die wir binden
  kind: Role
  name: deployment-manager
  apiGroup: rbac.authorization.k8s.io
```

Anwenden:

```
kubectl apply -f deployer-rolebinding.yaml
```

### 3.8 Erneuter Test (Erfolg erwartet)

Versuchen wir den `get pods` Befehl erneut mit der `sa-config.yaml`:

```
kubectl get pods --kubeconfig=sa-config.yaml
```

**Ergebnis:** `No resources found in rbac-uebung namespace.` Perfekt! Kein "Forbidden"-Fehler mehr. Der SA darf nun `Pods` auflisten.

Testen wir, ob er auch deployen darf (wir nutzen die Datei `writer-deployment.yaml` aus Übung 2):

```
kubectl apply -f writer-deployment.yaml --kubeconfig=sa-config.yaml
```

**Ergebnis:** `deployment.apps/writer-app created`. Es funktioniert!

**Aufräumen:**

```
rm sa-config.yaml
kubectl config set-context --current --namespace=default
kubectl delete namespace rbac-uebung
```
