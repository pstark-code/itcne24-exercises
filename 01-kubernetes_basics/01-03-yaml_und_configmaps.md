# YAML Resourcen und ConfigMaps/Secrets

Wir wollen jetzt diese Applikation auf Kubernetes deployen:

https://gitlab.com/tbz-itcne-msvc/blueprint-flask-prod/-/tree/main

Ihr dürft auch eure eigene Software nehmen, solange es mehr als einen Container gleichzeitig benötigt.

Diese Aufgabe ist besonders offen gestellt. Nehmt diese Gelegenheiten wahr um euer Recherchieren zu üben.

Bitte probiert es umzusetzen, und helft euch gegenseitig. Ich gebe euch noch ein paar Links zur relevanten Dokumentation. Und sonst googlet es oder fragt den AI-Assistant eurer Wahl. Überlegt euch dann aber gut, was ihr mit dem Resultat macht und wie ihr es überprüfen könnt. 

Kontrolliert eure AI, sonst kontrolliert die AI euch. ;)


## Einschub: Kubernetes Resourcen als YAML-Dateien

Mit `kubectl create deployment <DEPLOYMENT_NAME> --image=<IMAGE_NAME> --dry-run=client` könnt ihr ein `Deployment` ausgeben statt auf den Cluster zu deployen.

> [!TIP]
> Diese `--dry-run=client` Option könnt ihr bei fast allen kubectl Kommandos angeben um eine ähnliches Resultat zu erhalten. 

Jetzt könnt ihr das editieren um Sachen zu verändern, die ihr mit `create deployment` nicht setzen konnten. Und wenn ihr damit fertig seid, könnt ihr es mit `kubectl apply -f <DATEI_NAME>` dem Cluster übergeben. Und auch wenn ihr etwas daran verändert, könnt ihr einfach immer wieder `kubectl apply` ausführen. Wenn die Resource existiert, wird sie angepasst, wenn sie nicht existiert, wird sie angelegt.

Zum Beispiel also:

```shell
kubectl create deployment web-app --image=gitlab.com/tbz-hf/web-app-xyz --dry-run=client >deployment-web-app.yaml
kubectl apply -f deployment-web-app.yaml
```

> [!TIP]
> Eine bestehende Resource als YAML abspeichern könnt ihr mit 
> `kubectl get <TYPE> <NAME> -o yaml`

**Hilfestellungen zur Aufgabe:**

- Erstellt für jeden docker-compose service ein `Deployment`
- Erstellt zu jedem `Deployment` einen `Service`
- Benutzt für die Environment-Variablen eine ConfigMap oder ein Secret. Oder beides?
- es gibt ein Werkzeug namens [Kompose](https://kompose.io/), das docker-compose Dateien in Kubernetes Resourcen wandelt. 

**Dokumentation:**

- [kubectl_create_deployment](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_create/kubectl_create_deployment/)
- https://kubernetes.io/docs/concepts/configuration/configmap/
- https://kubernetes.io/docs/concepts/configuration/secret/
  - *Anmerkung aus dem Link*: Secrets are similar to ConfigMaps but are specifically intended to hold confidential data.
- [Tolle Referenz mit den JSON Schemas für viele Kubernetes Resourcen und was sie beinhalten können](https://kubespec.dev/)
- [JSON Schema für Pods](https://kubespec.dev/kubernetes/v1/Pod) 
- [JSON Schema für Deployments](https://kubespec.dev/kubernetes/apps/v1/Deployment)
- [JSON Schema für Services](https://kubespec.dev/kubernetes/v1/Service)
- [JSON Schema für ConfigMaps](https://kubespec.dev/kubernetes/v1/ConfigMap)
- [JSON Schema für Secrets](https://kubespec.dev/kubernetes/v1/Secret)

