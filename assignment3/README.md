
# ENSF 400 - Assignment 3: Kubernetes Deployment with Canary Strategy

## Introduction

This README outlines the process I followed to set up a canary deployment on Kubernetes using Minikube. It includes the actual terminal outputs as evidence of each step taken to prepare, deploy, and verify the application.

## Prerequisites

- Minikube installed and configured
- kubectl command-line tool

## Steps Taken

### Starting Minikube

Initialized Minikube, which set up a single-node Kubernetes cluster:

```
$ minikube start
```

#### Output

```
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ minikube start
😄  minikube v1.32.0 on Ubuntu 20.04 (docker/amd64)
✨  Automatically selected the docker driver. Other choices: none, ssh
📌  Using Docker driver with root privileges
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
💾  Downloading Kubernetes v1.28.3 preload ...
    > preloaded-images-k8s-v18-v1...:  403.35 MiB / 403.35 MiB  100.00% 107.06 
    > gcr.io/k8s-minikube/kicbase...:  453.90 MiB / 453.90 MiB  100.00% 102.78 
🔥  Creating docker container (CPUs=2, Memory=2200MB) ...
🐳  Preparing Kubernetes v1.28.3 on Docker 24.0.7 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔗  Configuring bridge CNI (Container Networking Interface) ...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🔎  Verifying Kubernetes components...
🌟  Enabled addons: default-storageclass, storage-provisioner
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```

### Enabling Ingress Addon

Enabled the Ingress addon to manage external access to services:

```
$ minikube addons enable ingress
```

#### Output

```
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ minikube addons enable ingress
💡  ingress is an addon maintained by Kubernetes. For any concerns contact minikube on GitHub.
You can view the list of minikube maintainers at: https://github.com/kubernetes/minikube/blob/master/OWNERS
    ▪ Using image registry.k8s.io/ingress-nginx/controller:v1.9.4
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v20231011-8b53cabe0
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v20231011-8b53cabe0
🔎  Verifying ingress addon...
🌟  The 'ingress' addon is enabled
```

### Deploying nginx and Application Components

Applied the Kubernetes manifest files for the nginx reverse proxy and both versions of the application:

```
$ kubectl apply -f nginx-dep.yaml
$ kubectl apply -f nginx-configmap.yaml
$ kubectl apply -f nginx-svc.yaml
$ kubectl apply -f app-1-dep.yaml
$ kubectl apply -f app-1-svc.yaml
$ kubectl apply -f app-2-dep.yaml
$ kubectl apply -f app-2-svc.yaml
$ kubectl apply -f app-1-ingress.yaml
$ kubectl apply -f app-2-ingress.yaml
```

#### Output

```
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f nginx-dep.yaml
deployment.apps/nginx-dep created
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f nginx-configmap.yaml
configmap/nginx-configmap created
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f nginx-svc.yaml
service/nginx-svc created
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f app-1-dep.yaml
deployment.apps/app-1-dep created
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f app-1-svc.yaml
service/app-1-svc created
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f app-2-dep.yaml
deployment.apps/app-2-dep created
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f app-2-svc.yaml
service/app-2-svc created
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f app-1-ingress.yaml
ingress.networking.k8s.io/app-1-ingress created
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ kubectl apply -f app-2-ingress.yaml
ingress.networking.k8s.io/app-2-ingress created
...
ingress.networking.k8s.io/app-2-ingress created
```

### Verifying Deployment with Minikube IP

Retrieved the IP address of Minikube and tested the application to confirm the canary deployment was functioning correctly:

```
$ minikube ip
```

#### Output

```
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ minikube ip
192.168.49.2
```

Tested the application's responses:

```
$ curl http://192.168.49.2/
```

#### Output

```
@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-2-dep-7f686c4d8d-snr8j]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-kmtwg]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-2-dep-7f686c4d8d-snr8j]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-kmtwg]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-2-dep-7f686c4d8d-snr8j]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-2-dep-7f686c4d8d-snr8j]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-kmtwg]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-kmtwg]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-kmtwg]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-kmtwg]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-kmtwg]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-kmtwg]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-kmtwg]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-1-dep-86f67f4f87-kmtwg]!@rajdeeepdas ➜ /workspaces/ensf400-lab8-kubernetes-2/assignment3 (main) $ curl http://192.168.49.2/
Hello World from [app-2-dep-7f686c4d8d-snr8j]!
...
```

## Conclusion

The application is now successfully deployed and accessible. The canary deployment strategy has been verified to be operational, with traffic being served by both the stable and canary versions of the app.
