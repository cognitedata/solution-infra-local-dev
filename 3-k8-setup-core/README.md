# K8s development services - core

Here you will find a collection of development services that mirror the core services that our solution infrastructure offers. The services are tailored for a local install (i.e. they may have slightly different configurations compared to our production infrastructure versions) and represent the same capabilities as our production versions. The idea is that you can use these services to support your local development workflow.

If you have chosen a full install via [Argo CD](../2-argo-cd/README.md), then all the tools will be available for you automatically. 

### Manual install of all core services

You can manually install all services via `./.install-all-services` kustomization.

Navigate to the folder `./.install-all-services` and run the following command:
```console
$ kubectl apply -k .
```
> The installation may report errors on the first run. In that case just re-run the install command.

In order to uninstall the services, run:
```console
$ kubectl delete -k .
```

### Manual install of single services

You can also deploy single services. In order to deploy a service manually, do the following:
1) Navigate to the folder of the service, for example:
```console
$ cd ./victoria-metrics
```

2) Run `kubectl apply -k .` to install the service/module.
```console
$ kubectl apply -k .
```

3) In order to uninstall/delete the service, run `kubectl delete -k .`
```console
$ kubectl delete -k .
```

## Services overview

### VictoriaMetrics / Prometheus

VictoriaMetrics is the Prometheus-compatible metrics and monitoring solution. It collects metrics (based on the Prometheus protocols), stores them and enables monitoring via alerting rules. 

- K8s cluster URL for the metrics storage: `http://vmsingle-vmsingle.victoria-metrics:8429`.
- K8s cluster URL for the push gateway: `http://prometheus-pushgateway.victoria-metrics:9091`.

You can access the VictoriaMetrics basic GUI at [http://victoria-metrics.localhost/vmui](http://victoria-metrics.localhost/vmui).

You can access the pushgateway basic GUI at [http://pushgateway.localhost](http://pushgateway.localhost).

### Grafana

We use Grafana for easy visualization / dashboarding of metrics from Prometheus / VictoriaMetrics.

You can access the main Grafana UI at [http://grafana.localhost](http://grafana.localhost).


### Emissary Ingress

Emissary is a helper component that enables us to expose services from K8s to the outside world. In the dev setup, this enables accessing the GUIs of our dev services on `http://<service-name>.localhost`.

### Echoserver

Echoserver is a simple service that we use to test the setup. If everything is working, it should give you a simple text response in your browser. 

You can access echoserver at [http://echoserver.localhost](http://echoserver.localhost)

## What's next

- Have fun developing containerized applications.
- Add more development services to enjoy functionality like `workflow support` and `event handling` by adding the `extended development services`from [../4-k8-setup-extended/README.md](../4-k8-setup-extended/README.md).