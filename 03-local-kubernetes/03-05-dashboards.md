# Übung: Dashboards

Installiert diese GUI/Webinterfaces und macht euch mit ihnen vertraut. Am Ende könnt ihr euch einfach für eines entscheiden. Diese Entscheidung überlasse ich euch.

## Kubernetes Dashboard

Links:
- [GitHub](https://github.com/kubernetes/dashboard)
- [Getting Started](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)

Das Kubernetes Dashboard ist ein simples aber effektives Dashboard um Resourcen in einem Kubernetes Cluster anzuschauen und einfache Verwaltungsaufgaben zu erfüllen.

Es kann in Minikube mit diesem Befehl installiert werden:

```
minikube addons enable dashboard
```

Und mit dem Befehl `minikube dashboard` wird das Kubernetes Dashboard im Browser geöffnet. (Anmerkung: Aus irgendwelchen Gründen wird in Minikube eine Version vom Dashboard verwendet, die mehrere Jahre alt ist. Soweit ich weiss ist der Unterschied in der Funktionalität aber nicht riesig.)

## Headlamp

Links:
- [GitHub](https://github.com/kubernetes/dashboard)
- [Get Headlamp for Desktop](https://headlamp.dev/#download-platforms)
- [Getting Started](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)

Als Alternative zum Kubernetes Dashboard gibt es noch ein etwas moderneres und ambitionierteres Projekt namens "Headlamp". Die UI nutzt ArgoCD-ähnliche Visualisierungen um die laufenden Applikationen darzustellen. Ein weiterer Vorteil ist dass Headlamp Plugins unterstützt.

Headlamp kann entweder lokal als Desktop Applikation installiert werden, oder im Kubernetes Cluster selber, und dann wird es per Browser angesteuert.



### Desktop Application

Die Installation auf verschiedenen Plattformen ist hier dokumentiert: https://headlamp.dev/#download-platforms


### In-Cluster (minikube)

Installieren mit:

```
minikube addons enable headlamp
```

Ein Auth-Token generieren, das 24 Stunden lang gültig ist mit:
```
kubectl create token headlamp --duration 24h -n headlamp
```

Zugreifen mit:
```
minikube service headlamp -n headlamp
```


Die Installation auf verschiedenen Plattformen ist hier dokumentiert: https://headlamp.dev/#download-platforms


## Lens

Links:
- [Get Lens for Desktop](https://k8slens.dev/)

Eine weitere beliebte Kubernetes-UI ist Lens. Eine lange Zeit war dies bei Weitem die beste GUI. 

Leider wurde diese Software vor ein paar Jahren "closed-source" und jetzt braucht es einen User-Account um die Software nutzen zu können. Das hinterliess bei vielen einen sauren Geschmack, und unter meinen Arbeitskolleg*innen kenne ich (fast) keine Lens-Benutzer mehr. Wer bei uns eine GUI möchte, verwendet entweder das Openshift-eigene Webinterface, das ArgoCD Webinterface oder nutzt die Headlamp Desktopapplikation.

