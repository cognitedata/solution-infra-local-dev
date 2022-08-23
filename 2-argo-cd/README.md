# Argo CD

Argo CD ([https://argo-cd.readthedocs.io/en/stable/](https://argo-cd.readthedocs.io/en/stable/)) is the tool we use to manage our (continuous) deployment pipeline. In short, Argo CD will read service and configuration files from Git and automatically deploy them to Kubernetes. 

You don't need to install Argo CD locally in order to support the development process--you can just as well perform manual deployments and configurations of your K8s cluster. But, Argo will help you automate the process and you will get a similar experience to the solution infrastucture.

Running `Argo CD` and the full development services requires 4 - 8 GB RAM and 2 x CPU.

## Install Argo CD

Argo CD is installed into your K8s cluster via a single command and a few simple configuration steps. We will install Argo CD into the namespace `argocd`.
```console
$ kubectl apply -k . 
```
This command will read the file `kustomization.yaml` and apply the specification/install.

> You may get the following error during install:
> ```console
> error: unable to recognize ".": no matches for kind "AppProject" in version "argoproj.io/v1alpha1"
> ```
> In this case, just re-run:
> ```console
> $ kubectl apply -k .
> ```

Depending on your network bandwidth, the installation may take some time to download all the required artifacts. You can check the status via 
```console
$ kubectl get deployments -n argocd

# Response when ArgoCD is not ready--notice the columns "READY" and "AVAILABLE"
NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
argocd-applicationset-controller   0/1     1            0           4m53s
argocd-dex-server                  0/1     1            0           4m53s
argocd-notifications-controller    0/1     1            0           4m53s
argocd-redis                       0/1     1            0           4m53s
argocd-repo-server                 0/1     1            0           4m53s
argocd-server                      0/1     1            0           4m53s

# Response when ArgoCD is ready
NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
argocd-applicationset-controller   1/1     1            1           32s
argocd-dex-server                  1/1     1            1           32s
argocd-notifications-controller    1/1     1            1           32s
argocd-redis                       1/1     1            1           32s
argocd-repo-server                 1/1     1            1           32s
argocd-server                      1/1     1            1           32s
```

After it set up, it will automatically begin to install the development services. This may take 5 - 15 minutes, depending on your network bandwidth. You can monitor the installation by listing the current namespaces (different services are installed in different namespaces). As the installation progresses, more and more namespaces will be listed:
```console
$ kubectl get ns

# Example response
NAME                      STATUS   AGE
argo                      Active   19m
argo-events               Active   19m
argocd                    Active   23m
cert-manager              Active   19m
default                   Active   26m
echoserver                Active   19m
emissary                  Active   22m
emissary-system           Active   22m
external-secrets          Active   19m
grafana-operator-system   Active   19m
kube-node-lease           Active   26m
kube-public               Active   26m
kube-system               Active   26m
victoria-metrics          Active   19m
``` 
Look for the namespaces `emissary` and `emissary-system`. When they are `active`, you can try accessing the ArgoCD GUI. [Emissary](../3-k8-setup-core/README.md#emissary-ingress) is the component that exposes the GUIs on `localhost`.
> ArgoCD GUI: [http://argocd.localhost](http://argocd.localhost)

## Configure ArgoCD

Before starting to use ArgoCD, we need to perform a few basic setup steps.

### Set new admin password
The first item, is to set a new password for the admin account. Argo is installed with a default password which you need for the initial login. 

Get the `base64` encoded default password.
```console
$ kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"
```
which you can then decode using for example [https://www.base64decode.org](https://www.base64decode.org)

Login to [http://argocd.localhost](http://argocd.localhost) with your browser, using the username `admin` and the `base64` decoded default password. Navigate to the `User Info` section ([http://argocd.localhost/user-info](http://argocd.localhost/user-info)) and update the password.

> In case you are unable to access ArgoCD on [http://argocd.localhost](http://argocd.localhost), we can temporarily port-forward the UI via `kubectl`. The following command will enable a temporary port forward of the UI to `http://localhost:8080`.
>```console
>$ kubectl port-forward svc/argocd-server -n argocd 8080:443
>```

Now ArgoCD is installed along with the development services. 

## What's next

- Access `ArgoCD` at [http://argocd.localhost](http://argocd.localhost).
- Setup your `GitHub` credentials so ArgoCD can read you private repos

### Configure read access to you GitHub repos

If you want ArgoCD to read private GitHub repositories (which is the default for our solution development repos), it needs to be able to authenticate itself as your GitHub user. 
- Generate a personal access token. Go to your settings in [GitHub --> settings --> developer settings --> personal access tokens](https://github.com/settings/tokens), and generate a new token. Remember to add `SSO` settings for the token.
- Add this token as the `password` in the `argocd-repo-creds.yaml` and apply it to the `argocd` namespace.
```console
$ kubectl -n argocd apply -f ./argocd-repo-creds.yaml
```
 Now you can point ArgoCD to private repos to read deployment manifests and perform continuous deployment to your local kubernetes.