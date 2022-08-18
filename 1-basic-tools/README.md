# Basic tools

Here you will find an overview of a set of basic tools that will help you with the development lifecycle for code and configuration. With the basic tools you have the minimum setup you need to develop and manage code and configuration. 

## Git

We use GitHub to host our code (and config) respositories. Install Git locally to help interact with the Git-based development flow: [https://git-scm.com/downloads](https://git-scm.com/downloads)

## Code IDE

Feel free to use the code editor of your choice. Popular choices include:
- VS Code ([https://code.visualstudio.com/](https://code.visualstudio.com/)). Supports a wide variaty of languages and formats via plug-ins.
- IntelliJ Community Edition ([https://www.jetbrains.com/idea/download](https://www.jetbrains.com/idea/download)). Excellent choice for Java-based development. IntelliJ provides very good code completion suggestions (much better than VS Code) which greatly eases the development experience.
- PyCharm Community Edition ([https://www.jetbrains.com/pycharm/download](https://www.jetbrains.com/pycharm/download)). Excellent choice for Python-based development.

## Local Kubernetes

The main building block of our solution infrastructure is self-contained code modules--primarily packaged as containers. We use Kubernetes to manage our containers and base many of our development/solution services on Kubernetes principles. 

We recommend setting up a local Kubernetes cluster on your machine to support the local development flow. This allows you to develop and test your code in an evironment that closely mirrors the solution infrastructure.

There are multiple options for installing a local Kubernetes environment with popular choices including Docker Desktop, Minikube, K3s, and more. 
- Windows: On Windows we recommend using either `Docker Desktop` which includes a vanilla K8s ([https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)) or `Minikube` ([https://minikube.sigs.k8s.io/docs/start/](https://minikube.sigs.k8s.io/docs/start/)).
- Mac: `Minikube` is a good choice ([https://minikube.sigs.k8s.io/docs/start/](https://minikube.sigs.k8s.io/docs/start/)).
- Linux: `Minikube` is a good choice ([https://minikube.sigs.k8s.io/docs/start/](https://minikube.sigs.k8s.io/docs/start/)). You can also consider `K3s` ([https://k3s.io/](https://k3s.io/)).

## Helm

Helm is a tool used to install Kubernetes based applications. We need Helm in order to install the [external-secrets-operator](../3-k8-setup/external-secrets-operator/README.md).
- Windows: Download the Helm binary from [https://github.com/helm/helm/releases](https://github.com/helm/helm/releases), unpack it and add it to `PATH`.
- Mac: Install via Homebrew
```console
brew install helm
``` 
- Linux: Depeding on which flavor (and package manager) you use:
```console
snap install helm --classic
```

You can also consult the Helm install guide: [https://helm.sh/docs/intro/install/](https://helm.sh/docs/intro/install/).

## Kubernetes "IDE"

The main method for interacting with Kubernetes, is the CLI tool `kubectl`. This tool is bundled with the local Kubernetes flavors mentioned [above](#local-kubernetes) and allows you to control all aspects of Kubernetes.

If you favor a more visual interaction with Kubernetes, you can use `Lens` ([https://k8slens.dev/](https://k8slens.dev/)) as a "Kubernetes IDE". `Lens` can make it easier to get started with Kubernetes as it offers you a nice GUI and easy access to common operations.

## Skaffold

Skaffold helps orchestate the development workflow for container-based development. It integrates with the rest of your toolchain (across languages, including Python and Java) and will handle code build, debugging, image build and distribution and deploying the container on Kubernetes. It also allows live observation and debugging of your code while running in Kubernetes.
- Install, all platforms: [https://github.com/GoogleContainerTools/skaffold/releases](https://github.com/GoogleContainerTools/skaffold/releases)

## Java development

### Java 11

We currently use Java 11 as our default version. This is version our Java SDK and Beam SDK are based on. Through 2022 we are looking to move to the next `LTS` version, Java 17 (which brings significant syntactic and performance improvements), but we need to verify compatibility with our SDKs first. 

We recommend using the free `Adoptium Temurin` Java version ([https://adoptium.net/](https://adoptium.net/)) which is the rebranded/new version of `OpenJDK`.
- Windows: On Win11 and new Win10 you can use the `winget` packaged manager:
```console
winget install EclipseAdoptium.Temurin.11
```
Alternatively, download Java 11 SDK "Temurin" from [https://adoptium.net/](https://adoptium.net/).
- Mac: Install via Homebrew
```console
brew tap homebrew/cask-versions
brew install --cask temurin11
```
- Linux: Depeding on which flavor (and package manager) you use:
```console
apt-get install temurin-11-jdk
yum install temurin-11-jdk
zypper install temurin-11-jdk
```

Tip: Remember to set the `JAVA_HOME` environment variable after install.

### Maven

Maven is a tool for managing Java development projects. It handles dependencies, build and testing workflows.
- Download for Windows and Mac: [https://maven.apache.org/download.cgi](https://maven.apache.org/download.cgi).
- Installation tips: [https://maven.apache.org/install.html](https://maven.apache.org/install.html).

## Python development

