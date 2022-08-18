# Kubernetes tutorial

Getting started with Kubernetes. This tutorial will walk you through the basics of getting started with Kubernetes.

### Before we begin

You need access to a Kubernetes cluster. Please see [../1-basic-tools/README.md#local-kubernetes](../1-basic-tools/README.md#local-kubernetes) for how to install a local Kubernetes cluster.

You may also want to install IDE development support to help you navigate the installation. `VS Code` and `IntelliJ` have very useful plugins for Kubernetes supoort. Another alternative is the [Kubernetes IDE Lens](../1-basic-tools/README.md#kubernetes-"ide").

## Create a Job

A `Job` creates one (or more) pods and executes their container(s). When the pod/container completes, the `Job` itself is complete. A `Job` is a good option for containers you want to execute once or on a regular basis (a `CronJob`), for example a batch extractor or a batch pipeline.

Run a Job executing the `whalesay` image. This image will output logs containing the input commands:
```console
$ kubectl apply -f .\whalesay.job.yaml
```

Wait until the job completes successfully:
```console
$ kubectl get job/whalesay

NAME       COMPLETIONS   DURATION   AGE
whalesay   1/1           14s        21s
```

Check the logs:
```console
$ kubectl logs job/whalesay

 ___________________________
< This is a Kubernetes Job! >
 ---------------------------
    \
     \
      \
                    ##        .
              ## ## ##       ==
           ## ## ## ##      ===
       /""""""""""""""""___/ ===
  ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~
       \______ o          __/
        \    \        __/
          \____\______/
```

Get information about the job:
```console
$ kubectl describe job/whalesay

-----nice and comprehensive description-------
```

## Create a deployment

A `Deployment` is (a set of) pods running continuously. This workload is often used for services (modules that are listening for input/events) and continuously running jobs.

Create a deployment executing the `echoserver` image. This image is a simple http server echoing the input request:
```console
$ kubectl apply -f .\echoserver.deployment.yaml
```

Check the deployment status:
```console
$ kubectl get deployment/echoserver

NAME         READY   UP-TO-DATE   AVAILABLE   AGE
echoserver   1/1     1            1           30m
```

Check the pods:
```console
$ kubectl get pods

echoserver-5594f98d9-v7w9f   1/1     Running     0          31m
whalesay--1-8968h            0/1     Completed   0          16h
whalesay--1-q6szm            0/1     Completed   0          16h
```

Check the events:
```console
$ kubectl get events
```

Inspect the deployment:
```console
$ kubectl describe deployment/echoserver

-----nice and comprehensive description-------
```

## Create a service

The `deployment` is tied to the individual pods. If the deployment represents a service (a "function" / http endpoint), then we typically want to expose it as a... `service`, which gives it a stable address to be accessed at.

Deploy the service to expose the echoserver deployment:
```console
$ kubectl apply -f .\echoserver.service.yaml
```

Check the status:
```console
$ kubectl get service/echoserver

NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
echoserver   ClusterIP   10.111.222.252   <none>        8080/TCP   99s
```
We see that the service has a stable, (cluster) internal IP address that it can be reached at.

Let's expose this service outside the cluster so we can access it for testing:
```console
$ kubectl port-forward service/echoserver 8080:8080
```
Navigate to [http://localhost:8080/](http://localhost:8080/) with your web browser or PostMan and observe the responses

## Clean up

Let's remove the resources we just created:
```console
$ kubectl delete -f .\echoserver.service.yaml
$ kubectl delete -f .\echoserver.deployment.yaml
$ kubectl delete -f .\whalesay.job.yaml
```