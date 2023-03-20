# External Secrets

`External secrets` bridges specialized secrets' stores (`GCP Secret Manager`, `AWS Secrets Manager`, `Azure Key Vault`, etc.) and K8s generic `Secret` concept. This allows you to write your code without relating to the specialized secrets stores and just use the generic K8s concepts instead; i.e. read secrets via mounted environment variables or config files.

## Install External Secrets

If you run the full development services (via [ArgoCD](../../2-argo-cd/README.md)) or have manually installed the [core development services](../README.md), then `external secrets` are already installed. 

Alternatively you can manually install `external secrets` via a single command as described below. 

>### Prerequisites
> You need to have the `Helm` CLI installed on your system. Please refer to the basic tools documentation for installation instructrions: [../../1-basic-tools/README.md#helm](../../1-basic-tools/README.md#helm)


Navigate to the `./external-secrets-operator` folder and execute:
```console
$ kubectl kustomize --enable-helm . | kubectl apply -f -
```

## Configure external secrets

Configuring `external secrets` consists of setting up two, main resources:
1) `SecretStore` / `ClusterSecretStore`. The `secret store` configures the parameters of the external secret store (`GCP Secret Manager`, `Azure Key Vault`, etc.). This is a one time configuration job per secret store. See below for how to configure `GCP Secret Manager` and `Azure Key Vault`.
2) `ExternalSecret`. The `external secret` defines a specific secret to read from the external store and map to a K8 `Secret` in the cluster (so your modules can access it). This step is repeated for each individual (or group of) secret.

### GCP Secret Manager

First we aquire the access credentials/service account to `secret manager` and store them in a K8s `secret`. Secondly, we define the `ClusterSecretStore` manifest and reference the credentials from the K8s `secret`.

1) Get the GCP service account with read access (`secret accessor`) to the GCP Secret Manager (secrets). Download the account certificate (JSON).
2) Add the Json certificate to the `gcpsm.secret.yaml` secret manifest.
3) Add the secret to your local K8s:
```console
$ kubectl apply -f ./gcpsm.secret.yaml
```

4) Add the GCP project name to the `gcpsm-template.clustersecretstore.yaml` manifest.
5) Add the secret store manifest to your local K8s:
```console
$ kubectl apply -f ./gcpsm-template.clustersecretstore.yaml
```

### Azure Key Vault

First we aquire the access credentials/service account to `key vault` and store them in a K8s `secret`. Secondly, we define the `ClusterSecretStore` manifest and reference the credentials from the K8s `secret`.

1) Get the Azure service principal with read access to the Azure Key Vault.
2) Add the client ID and client secret to the `azurekv.secret.yaml` secret manifest.
3) Add the secret to your local K8s:
```console
$ kubectl apply -f ./azurekv.secret.yaml
```

4) Add the Azure tenant ID and Vault URL to the `azurekv-template.clustersecretstore.yaml` manifest.
5) Add the secret store manifest to your local K8s:
```console
$ kubectl apply -f ./azurekv-template.clustersecretstore.yaml
```

### External Secret

External secret manifest examples:
- `gcpsm-template.externalsecret.yaml` illustrates how to define a secret sourced from `GCP Secret Manager`.
- `azurekv-template.externalsecret.yaml` illustrates how to define a secret sourced from `Azure Key Vault`.