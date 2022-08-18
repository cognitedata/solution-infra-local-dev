# Solution delivery, local development setup

Setting up a local, K8-based dev environment for solution delivery.

Solution delivery projects are based on handling code and configuration items. Efficient and fun deliveries depend on good tooling that supports you in handling the full life-cycle of code. This guide will help you get started with setting up a local development environment aligned with our solution delivery guidelines.

There are three, main steps:
1. Basic tools. The local coding environment and container support.
2. (Optional) Core infrastructure services. These are the core services (from our foundational infrastructure) like monitoring and secrets handling that help you with local development.
3. (Optional) Extended infrastructure services. These are the complete services that you get from our production infrastructure. Eg. workflows, event handling, continuous deployment, etc.

## Basic tools

You should install a set of basic tools that will help you with the development lifecycle for code and configuration. With the basic tools you have the minimum setup you need to develop and manage code and configuration. Please have a look at [1-basic-tools](1-basic-tools/README.md) for an overview.

Some parts of the development workflow may benefit from extra tooling. For example, handling secrets, authoring workflows, handling metrics and alterting. In these cases you may want to explore the additional development services below.

## Infrastructure / development services

Our solution infrastructure provides a set of services to help you develop, host, monitor and maintain solutions. This includes `workflows / orchestration`, `handling secrets`, `metrics and monitoring`, etc. Most of these services are hosted on our cloud Kubernetes environments and we can deploy local flavors of it to support local development.

There are two ways to install the development services locally:
1. Manual install of selected services. This the recommended alternative for most scenarios.
2. Automated install (and management) of the full services stack. Offers "all you can eat" with automated updates, but eats a bit of resources from your dev machine (4 - 6GB RAM, 1 x CPU).

### Manual install of selected services

You can manually install selected development services. A manual install is as simple as running a single command--you can also uninstall a service with a single command. You can install/uninstall services as many times as you want without the risk of "polluting" your local machine since everything is containerized.

This way, you can tailor your setup towards your specific development needs and have a setup with low overhead. As a starting point, we recommend installing the core infrastructure services.

Install:
- Recomended: Go to [./3-k8-setup-core/README.md](./3-k8-setup-core/README.md) to install the core services including metrics and monitoring and follow the instructions. There you will find more information on the different development services/tools and how to enable them. Enjoy!
- Optionally, add the extended development services from [./4-k8-setup-extended/README.md](./4-k8-setup-extended/README.md). This will add more load to you system, but you also get support for running workflows and event handling.

### Automated install of all services

The automated install will use a local `Argo CD` (our automated deployment tool) to install all development services and continuously monitor them. `Argo CD` will monitor the development services Github repository and automatically install updates for you. 

This option gives you (nearly) all the services and automated management of them. The main drawback is that hosting all services locally can tax your computer's resources. Running `Argo CD` and the full development services requires 4 - 8 GB RAM and 2 x CPU.

One alternative to avoid continuously taxing your computer, is by stopping/suspending the local K8s cluster hosting the dev services when you don't need them. Then you can start/resume it when needed. Both `Docker` and `Minikube` supports stop/start.

Install:
- Go to [2-argo-cd](2-argo-cd/README.md) and follow the instructions. This step installs `Argo CD` which will help manage your dev services.