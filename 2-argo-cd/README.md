# Argo CD

Argo CD ([https://argo-cd.readthedocs.io/en/stable/](https://argo-cd.readthedocs.io/en/stable/)) is the tool we use to manage our (continuous) deployment pipeline. In short, Argo CD will read service and configuration files from Git and automatically deploy them to Kubernetes. 

You don't need to install Argo CD locally in order to support the development process--you can just as well perform manual deployments and configurations of your K8s cluster. But, Argo will help you automate the process and you will get a similar experience to the solution infrastucture.

Running `Argo CD` and the full development services requires 4 - 8 GB RAM and 2 x CPU.

## Install Argo CD

Argo CD is installed into your K8s cluster via a single command and a few simple configuration steps. We will install Argo CD into the namespace `argocd` along with the extension "application set controller".
```console
kubectl apply -k . 
```
This command will read the file `kustomization.yaml` and apply the specification/install.

> You may get the following error during install:
> ```console
> error: unable to recognize ".": no matches for kind "AppProject" in version "argoproj.io/v1alpha1"
> ```
> In this case, just re-run:
> ```console
> kubectl apply -k .
> ```

Depending on your network bandwidth, the installation may take some time to download all the required artifacts. You can check the status via 
```console
kubectl get deployments -n argocd

# Response when not ready--notice the columns "READY" and "AVAILABLE"
NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
argocd-applicationset-controller   0/1     1            0           4m53s
argocd-dex-server                  0/1     1            0           4m53s
argocd-notifications-controller    0/1     1            0           4m53s
argocd-redis                       0/1     1            0           4m53s
argocd-repo-server                 0/1     1            0           4m53s
argocd-server                      0/1     1            0           4m53s

# Response when ready
NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
argocd-applicationset-controller   1/1     1            1           32s
argocd-dex-server                  1/1     1            1           32s
argocd-notifications-controller    1/1     1            1           32s
argocd-redis                       1/1     1            1           32s
argocd-repo-server                 1/1     1            1           32s
argocd-server                      1/1     1            1           32s
```

## Configure ArgoCD

Before starting to use ArgoCD, we need to perform a few basic setup steps.

### Set new admin password
The first item, is to set a new password for the admin account. Argo is installed with a default password which you need for the initial login. 

Get the `base64` encoded default password.
```console
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}"
```
which you can then decode using for example [base64decode](https://www.base64decode.org)

Expose the ArgoCD UI to localhost so you can login and access the Argo functionality. The following command will enable a temporary port forward of the UI to `http://localhost:8080`.
```console
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Login to `http://localhost:8080` with your browser, using the username `admin` and the `base64` decoded default password. Navigate to the `User Info` section ([http://localhost:8080/user-info](http://localhost:8080/user-info)) and update the password.

### Configure read access to you GitHub repos

ArgoCD need read access to the GitHub repositories so it can read the source manifests (the definitions/configurations to apply to K8s). 
- Generate a personal access token. Go to your settings in [GitHub --> settings --> developer settings --> personal access tokens](https://github.com/settings/tokens), and generate a new token.
- Add this token as the `password` in the `argocd-repo-creds.yaml` and apply it to the `argocd` namespace.
```console
kubectl -n argocd apply -f ./argocd-repo-creds.yaml
```

### Add the dev services repository to ArgoCD

Let's add the dev services repositoy to ArgoCD so that it will be automatically set up for you. Argo will monitor the services definitions in the repository and install everything and keep it up to date continuously. 

During the installation, we set up a `project` (and Argo CD grouping of applications) to host the dev. services. Now we will add an `application set` definition to this `project`. The `application set` configures Argo to monitor the dev services repository and install all the services/applications defined there.
```console
kubectl -n argocd apply -f ./argocd-applicationset.yaml
```

## What's next

`ArgoCD` will start installing all the development services, including the configuration for accessing its GUI.
- Access `ArgoCD` at [https://argocd.localhost](https://argocd.localhost).