# Secret Management (Work in Progress!!)

First we need a secret provider, so we'll install OpenBao

## Hashicorp Vault / OpenBao

- [Getting Started with OpenBao on Kubernetes](https://openbao.org/docs/platform/k8s/helm/run/)
- Helm Chart GitHub https://github.com/openbao/openbao-helm/tree/main/charts/openbao

## Secrets Store CSI Driver

Links:
- [Installation mit Helm](https://secrets-store-csi-driver.sigs.k8s.io/getting-started/installation)
- [Usage](https://secrets-store-csi-driver.sigs.k8s.io/getting-started/usage)



```shell
helm repo add secrets-store-csi-driver https://kubernetes-sigs.github.io/secrets-store-csi-driver/charts
```

Um ein Secret mounten zu können brauchen wir eine SecretProviderClass:

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: openbao
spec:
  provider: vault
  parameters:
    roleName: "csi"
    vaultAddress: "http://vault.vault:8200"
    objects:  |
      - secretPath: "secret/data/foo"
        objectName: "bar"
        secretKey: "bar"
      - secretPath: "secret/data/foo1"
        objectName: "bar1"
        secretKey: "bar1"
```
