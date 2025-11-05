# Übungsreihe Modulblock 03 - Einführung Kubernetes Verwalten


## Lernziele

- Ich kann mit KinD oder Minikube eine Kubernetes Instanz auf meinem Laptop erstellen
  - 

- Ich verstehe wie ich Zugriff auf meinen Kubernetes Cluster verwalten kann mit diesen Werkzeugen und Resourcen:
  - Roles, RoleBindings
  - ClusterRoles, ClusterRoleBindings
  - ServiceAccounts
  - Groups

- Ich kenne:
  - Kubernetes Dashboard
  - Headlamp (Lokal oder in-cluster als Webapplikation)
  - Lens

- Ich verstehe die Vor- und Nachteile von verschiedenen Secret Management Techniken:
  - "Manuell" Secrets erstellen 
  - Secret Injection/Sync mit dem [ExternalSecrets Operator](https://external-secrets.io/latest/)
  - [SealedSecrets](https://github.com/bitnami-labs/sealed-secrets)
  - [Secrets Store CSI Driver](https://secrets-store-csi-driver.sigs.k8s.io/introduction)


## Ablauf

1. Grundlagen für `kubectl` ([01-01-kubectl.md](./01-01-kubectl.md))
2. Applikationen starten mit Pods und Services ([01-02-pods_und_services.md](./01-02-pods_und_services.md))
3. Konfiguration mit ConfigMaps und Secrets
4. Skalieren mit Deployments
5. Dauerhafte Datenablage mit PersistentVolumes

