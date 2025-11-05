## Übung 4: User Accounts, ClusterRoles und ClusterRoleBindings

**Ziel:** Verstehen, wie _unsere eigenen_ Benutzerrechte (als "Menschen") im Cluster definiert sind.

Kubernetes verwaltet selbst keine User-Accounts. Es prüft nur, ob ein User (z.B. via `kubeconfig`-Zertifikat oder Token) gültig ist. Die _Autorisierung_ (was darf der User) passiert aber wieder über RBAC.

### 4.1 `ClusterRole` und `ClusterRoleBinding` erklärt

- **`ClusterRole`:** Wie eine `Role`, aber **nicht** an einen `Namespace` gebunden. Sie gilt für das _gesamte Cluster_. Sie wird für Cluster-weite Ressourcen (wie `Nodes`, `Namespaces` selbst) oder zur Vergabe von Rechten _über alle Namespaces hinweg_ genutzt (z.B. "darf `Pods` in _jedem_ `Namespace` sehen").
    
- **`ClusterRoleBinding`:** Bindet ein Subjekt an eine `ClusterRole`. Gilt ebenfalls für das ganze Cluster.
    

### 4.2 Eigenen User identifizieren

Mit welchem User arbeiten _wir_ (die Admins) die ganze Zeit? Schauen wir in unsere `kubeconfig`:

```shell
kubectl config view --minify
```

Sucht den `user` der unter dem `current-context` (`minikube`) eingetragen ist. Der User-Name ist meist `minikube` oder (falls ihr Docker Desktop nutzt) `docker-desktop`. Dieser User ist (meist) über ein Client-Zertifikat authentifiziert.

Alternativ gibt es die `get-contexts` View:

```shell
# Der aktive Context ist in der Spalte CURRENT mit einem "*" markiert. In der Spalte NAME findest du den Usernamen.
kubectl config get-contexts
```

### 4.3 `ClusterRoleBindings` und `ClusterRoles` finden

Warum dürfen wir (als dieser User) alles? Wir müssen an eine mächtige `ClusterRole` gebunden sein.

Finden wir den User-Namen (bei mir `minikube`):

```shell
USER_NAME=minikube  # Mit eurem Usernamen ersetzen, falls anders.
```

_(Hinweis: Wenn euer Context nicht 'minikube' heißt, müsst ihr den Befehl anpassen.)_

Suchen wir nach `ClusterRoleBindings`, die diesen User als Subjekt haben:

```
kubectl get clusterrolebindings -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{range .subjects[*]}{.kind}{"::"}{.name}{"\t"}{end}{"\n"}{end}' | grep $USER_NAME
```

Ihr werdet sehr wahrscheinlich ein Binding sehen, z.B. `minikube-cluster-admin` oder `docker-desktop-admin`.

Schauen wir uns dieses `ClusterRoleBinding` genauer an (ersetzt `<NAME>` mit dem gefundenen Namen):

```
kubectl get clusterrolebinding <NAME> -o yaml
```

Ihr werdet sehen, dass dieses Binding (z.B. `minikube-cluster-admin`) euren User (`subjects: ... name: minikube`) an die `ClusterRole` namens `cluster-admin` bindet (`roleRef: ... name: cluster-admin`).

Was ist die `ClusterRole` "cluster-admin"?

```
kubectl get clusterrole cluster-admin -o yaml
```

**Ergebnis:**

```
rules:
- apiGroups:
  - '*'
  resources:
  - '*'
  verbs:
  - '*'
- nonResourceURLs:
  - '*'
  verbs:
  - '*'
```

Das bedeutet: "Alle API-Gruppen, alle Ressourcen, alle Verben". Das ist der "Gott-Modus" im Cluster. Weil `minikube` unser lokales Entwicklungs-Cluster ist, gibt es uns standardmäßig volle Admin-Rechte.