# 1.1. Grundlagen zu `kubectl` 

In diesem Abschnitt lernen wir die Grundlagen vom Standardwerkzeug `kubectl` kennen.

## Kubectl Konfigurieren

Für die Konfiguration sucht kubectl nach einer Datei namens `config` im Verzeichnis `$HOME/.kube`. Ihr könnt andere kubeconfig-Dateien angeben, indem ihr die Umgebungsvariable KUBECONFIG setzen oder die --kubeconfig Flag verwenden.

Wir schauen uns in dieser Übung einen limitierten Satz an Kommandos an. Weiterführende Links sind am Ende dieses Dokuments.

### Grundsyntax von kubectl Kommandos

```shell
kubectl [befehl] [TYP] [NAME] [flags]
```

wobei befehl, TYP, NAME und flags Folgendes sein können:

* `befehl`: Gibt die Operation an, die Sie an einer oder mehreren Ressourcen durchführen möchten, zum Beispiel create, get, describe, delete.

* `TYP`: Gibt den Ressourcentyp an. Ressourcentypen sind nicht case-sensitive und Sie können die Singular-, Plural- oder abgekürzte Form angeben. Die folgenden Befehle erzeugen beispielsweise dieselbe Ausgabe:  
    ```shell
    kubectl get pod pod1  
    kubectl get pods pod1  
    kubectl get po pod1
    ```
* `NAME`: Gibt den Namen der Ressource an. Bei Namen wird zwischen Gross- und Kleinschreibung unterschieden. Wenn der Name weggelassen wird, werden Details für alle Ressourcen mit dem Typen `TYP` angezeigt, zum Beispiel `kubectl get pods`.

* `flags`: Gibt optionale Flags an. Sie können beispielsweise die Flags `-n` oder `--namespace` verwenden, um in einem anderen Namespace zu handeln.

Jetzt gehts weiter in der [Übung 1.2](./01-02-pods_und_services.md). Falls ihr einmal feststeckt, schaut euch die Links hier unten an, oder stellt einfach eine Frage.

## Dokumentation und Tips

Offizielle Referenzen zu `kubectl`:
- [Introduction to kubectl](https://kubernetes.io/docs/reference/kubectl/introduction/)
- [kubectl für Docker-Benutzer](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)
- [kubectl Overview](https://kubernetes.io/docs/reference/kubectl/)
- [kubectl Subkommandos](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)
- [kubectl man-page](https://kubernetes.io/docs/reference/kubectl/kubectl/)
- [Deutscher Spickzettel zu kubectl](https://kubernetes.io/de/docs/reference/kubectl/cheatsheet/)

