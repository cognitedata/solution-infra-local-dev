# K8s development services - extended

Here you will find the extended set of development services that complement the core services at [../3-k8-setup-core/README.md](../3-k8-setup-core/README.md). The services are tailored for a local install (i.e. they may have slightly different configurations compared to our production infrastructure versions) and represent the same capabilities as our production versions. The idea is that you can use these services to support your local development workflow.

If you have chosen a full install via [Argo CD](../2-argo-cd/README.md), then all the tools will be available for you automatically and you don't need to manually install them. 

For manuall installation, please follow the instructions below. 

### Manual install of all core services

You can manually install all services via `./.install-all-services` kustomization. You need `Helm`installed on your system in order to run the full installation (please refer to [1-basic-tools#Helm](../1-basic-tools/README.md#helm)).

Navigate to the folder `./.install-all-services` and run the following command:
```console
$ kubectl kustomize --enable-helm . | kubectl apply --server-side -f -
```
> The installation may report errors on the first run. In that case just re-run the install command.

In order to uninstall the services, run:
```console
$ kubectl kustomize --enable-helm . | kubectl delete -f -
```

### Manual install of single services

You can also deploy single services. In order to deploy a service manually, do the following:
1) Navigate to the folder of the service, for example:
```console
$ cd ./victoria-metrics
```

2) Run `kubectl apply -k .` to install the service/module.
```console
$ kubectl apply --server-side -k .
```

3) In order to uninstall/delete the service, run `kubectl delete -k .`
```console
$ kubectl delete -k .
```

## Services overview

### Argo Workflows

Argo Workflows [https://argoproj.github.io/argo-workflows/](https://argoproj.github.io/argo-workflows/) is our workflow engine. It allows you to orchestrate a set of tasks to be executed. These tasks can be containers, http calls, scripts, etc. We use this as the basis for defining `data applications` that executes a set of operations on data. For example, a data pipeline that executes a series of processing steps.

You can access Argo Workflows at [https://argo.localhost](https://argo.localhost)

### Argo Events

Argo Events [https://argoproj.github.io/argo-events/](https://argoproj.github.io/argo-events/) is the eventing sibling to `Argo Workflows`. It is an event-driven framework for Kubernetes that enables you to trigger workflows, serverless functions, etc. from a variety of event sources.

### External Secrets

[External secrets](./external-secrets-operator/README.md) bridges specialized secrets' stores (`GCP Secret Manager`, `AWS Secrets Manager`, `Azure Key Vault`, etc.) and K8s generic `secret` concept. This allows you to write your code without relating to the specialized secrets stores and just use the generic K8s concepts instead; i.e. read secrets via mounted environment variables or config files.

`External secrets` must be configured to read secrets from the secrets' store you are using. A configuration guide and templates are described at [./external-secrets-operator/README.md](./external-secrets-operator/README.md).