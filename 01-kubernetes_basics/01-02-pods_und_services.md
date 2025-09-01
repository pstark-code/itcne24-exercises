# Applikationen starten mit Pods und Services

Um Kubernetes nutzen zu können, müssen wir Container starten und verwalten können. Hier lernen wir, wie wir das mit kubectl machen.

## Container Starten mit `kubectl run`

Um einen Container zu starten müssen wir einen `Pod` erstellen. 

Als Platzhalter- oder Beispiel-Applikation verwenden wir diesmal [Podinfo](https://github.com/stefanprodan/podinfo). Das hat den Vorteil eines knuffigen Maskott--ich mach nur Witze. Diese Applikation zeigt unter anderem an, wie der Pod heisst, der gerade antwortet. Damit können wir ein paar 

```shell
kubectl run kubectl-run-test --image=stefanprodan/podinfo:6.9.1 --port=9898
```

Okay, was jetzt?? Mit dem nächsten Kommando können wir einen Port von unserem Rechner bis zu einem Pod durchschleusen:

## Port durchschleusen mit `kubectl port-forward`

```shell
kubectl port-forward pods/kubectl-run-test 8000:9898
```

Der Port links vom Doppelpunkt ist lokal, der Port rechts davon ist am Pod. Das heisst wir können jetzt mit dem Browser darauf zugreifen. Der Hostname ist `http://localhost` und der Port ist `8000`. 

> [!TIP]
> (Also [http://localhost:8000](http://localhost:8000))

Macht euch etwas mit der einfachen Applikation vertraut. Der Pod-Name wird angezeigt. Um weitere Infos anzuzeigen gibt es noch ein paar API-Endpunkte. Eine vollständige Liste findet ihr [auf der Podinfo-Webseite](https://github.com/stefanprodan/podinfo). Wir schauen uns hier einen speziell nützlichen an um Umgebungsvariablen anzuzeigen: [/env](http://localhost:8000/env)

> [!TIP]
> Unter der Variable `HOSTNAME` ist der Name vom Pod zu finden.

> [!NOTE]
> `kubectl port-forward` ist ein debugging-Werkzeug und eignet sich nicht für den produktiven Betrieb.

Probiert noch weitere API Endpunkte aus. Meldet interessante Resultate im gemeinsamen Whiteboard

Vorschläge:
- GET /version
- GET /metrics
- GET /healthz
- GET /readyz
- GET /headers
- GET /configs

> [!TIP]
> Wenn ihr die gleiche URL wie im Browser (`http://localhost:8000/`) stattdessen per `curl` aufruft kommt etwas anderes zurück. Schreibt es ins Whiteboard.

## Resourcen inspizieren mit `kubectl describe` und `k9s`

Im untenstehenden Befehl müsst ihr noch den Type und den Namen ersetzen. Wir wollen den Pod aus dem vorherigen Abschnitt anschauen. 

```shell
kubectl describe TYPE NAME
```

Was könnt ihr hier sehen? Es ist wahrscheinlich, dass euch für vieles noch der Kontext fehlt. Überlegt euch trotzdem was die verschiedenen Werte bedeuten könnten. Wir besprechen das gleich im Plenum

<!-- 
## Eine Applikation Cluster-weit zugänglich machen mit `kubectl expose`

Um jetzt das Load-Balancing von unserem Service zu sehen, starten wir einen temporären Pod innerhalb von unserem Namespace:

```shell
kubectl run my-debug-shell --rm -i -t --image=docker.io/nicolaka/netshoot
```

Das Container-Image `docker.io/nicolaka/netshoot` ist ein praktisches Netzwerk-Debugging-Image, das viele Werkzeuge mitbringt, darunter `ping` und `curl`. 

> [!NOTE]
> Dank der `--rm` Flag wird dieser Pod gelöscht sobald wir das Terminal schliessen. Und mit den `-i` und `-t` Flags wird der Pod im Vordergrund gestartet, damit wir damit arbeiten können. -->


## Dokumentation und Tips

Kurze Anmerkung: Auf der https://kubernetes.io/ Webseite gibt es auch deutsche Seiten, aber die sind leider etwas spärlich vorhanden und nicht immer aktuell gehalten. Oben rechts auf der Seite hat es einen Sprach-Auswähler. Probiert es einfach aus.

Grundsätzlich würde ich euch aber empfehlen, euch an die Englische Dokumentation zu gewöhnen. Ihr werdet wahrscheinlich nicht darum herum kommen, also fangt doch gleich damit an. ;-)

Dokumentation:
- [Pods](https://kubernetes.io/docs/concepts/workloads/pods/)
- [Lebenszyklus von Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/)

Verwendete Software:
- [Netshoot](https://github.com/nicolaka/netshoot)
- [Podinfo](https://github.com/stefanprodan/podinfo)