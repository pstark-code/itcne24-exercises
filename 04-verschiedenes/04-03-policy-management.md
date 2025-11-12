# Policy Management (WIP)

Manchmal reichen die RBAC-basierenden Einschränkungen nicht aus, um alles abzubilden was erlaubt oder nicht erlaubt sein soll. Mit Kyverno können auch komplexe Regeln abgebildet werden. Diese Regeln können auch auf den Inhalt der Ressourcen schauen, und nicht nur den Namespace und den Typ der Resource. 

Damit können wir beispielsweise verhindern, dass von einem User zu viele Ressourcen aufs Mal reserviert werden. 

Zusätzlich können diese Regeln nicht nur die Erstellung, sondern auch die Mutation von Resourcen einschränken.

Anmerkung: Es gibt inzwischen auch Kubernetes-Native Werkzeuge dafür, die [ValidatingAdmissionPolicy](https://kubernetes.io/docs/reference/access-authn-authz/validating-admission-policy/) und die [MutatingAdmissionPolicy](https://kubernetes.io/docs/reference/access-authn-authz/mutating-admission-policy/). Letzteres ist jetzt noch in der Beta Phase.

Da aber Kyverno (und OPA als alternative) schon 

Links:

- [ValidatingAdmissionPolicy](https://kubernetes.io/docs/reference/access-authn-authz/validating-admission-policy/)
- [MutatingAdmissionPolicy](https://kubernetes.io/docs/reference/access-authn-authz/mutating-admission-policy/)
- [Kyverno GitHub](https://github.com/kyverno/kyverno)
- [Kyverno Playground](https://playground.kyverno.io/#/)


## 0. Für Neugierige

- Öffnet den Kyverno Playground
- Links oben hat es ein Menu Symbol, da findet ihr Beispiele und Tutorials. 
- Wählt etwas aus und drückt "Start" unten.
- Es dauert ein paar Sekunden bis das Resultat angezeigt wird. Bei meinem Beispiel waren zwei Ressourcen dabei, eine wurde angenommen und die andere nicht. 
- Verändert etwas und schaut wie sich das Resultat verhält.
- Lest die Policies links und die Ressourcen rechts durch. 

## Work in Progress