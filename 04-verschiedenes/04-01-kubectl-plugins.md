# Plugins für `kubectl` 

Kubectl hat uns bisher gute Dienste geleistet, aber seine Funktionalität ist etwas limitiert. 

Glücklicherweise unterstützt es Plugins. Am einfachsten können diese mit `krew` installiert werden.

## 1. `krew` installieren

Links:
- [Install krew (official docs)](https://krew.sigs.k8s.io/docs/user-guide/setup/install)

Windows (laut [winstall.app](https://winstall.app/apps/Kubernetes.krew)):
```powershell
winget install --id=Kubernetes.krew  -e
```
Wenn das nicht funktioniert, könnt ihr die "manuelle" Variante in der Dokumentation machen.

MacOS/Linux: Der offiziellen Doku folgen.


## 2. Plugins Suchen mit `krew`

Folgt dem [Quickstart](https://krew.sigs.k8s.io/docs/user-guide/quickstart/) um ein Plugin zu installieren und testen ob es funktioniert und es danach wieder zu deinstallieren.

## 3. Erprobte oder Empfohlene Plugins

Mit `kubectl krew search` könnt ihr den Index durchsuchen. Es gibt auch eine web-basierte Variante von [dem Index](https://krew.sigs.k8s.io/plugins/).

Schaut euch die Liste an. Ich kann empfehlen, einfach Plugins auszuprobieren, gerade wenn sie viele Sterne haben. Da ist meiner Erfahrung nach die Wahrscheinlichkeit grösser, dass sie gut funktionieren und einen allgemeinen Nutzen haben.

Meine Empfehlungen:

- neat - Clean up Kubernetes yaml and json output to make it readable
- tree - kubectl plugin to browse Kubernetes object hierarchies as a tree
- view-secret - Kubernetes CLI plugin to decode Kubernetes secrets
- ktop - Top-like tool for your Kubernetes clusters
- graph - Kubectl plugin to visualize Kubernetes resources and relationships
- example - kubectl plugin for retrieving resource example YAMLs

Aber ich würde euch **stark** empfehlen, die Liste durchzuscrollen und zu schauen ob euch eine Beschreibung besonders zusagt. Vielleicht findet ihr ja das Tool, das euch die Semesterarbeit einfacher macht.




