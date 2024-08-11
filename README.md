# Kubernetes Fundamentals

- [Introduction](#introduction)
  - [Lab setup](#lab-setup)
- [Mealie](#mealie)
  - [Notes](#notes)
- [Homarr](#homarr)
  - [Installing Homarr using Helm](#installing-homarr-from-a-helm-chart)
- [Monitoring](#monitoring)
  - [Add the Helm repository](#add-the-helm-repository)
  - [Install the Helm Chart](#install-the-helm-chart)
- [Linkding](#linkding)
  - [Notes](#notes-1)

## Introduction

This repository contains my studies and experiments on Kubernetes. Most of the notes and exercises here were taken from [Mischa van den Burg's](https://mischavandenburg.substack.com/) Kubernetes Fundamentals course.

### Lab setup

For these labs, I'm using [Rancher Desktop](https://rancherdesktop.io/), running on Ubuntu 24.04. I'm also using [k9s](https://k9scli.io/), a super cool terminal based UI. Try it out!

## Mealie

The first project is to run [Mealie](https://github.com/mealie-recipes/mealie) in Kubernetes. Mealie is a self-hosted recipe manager. The Mealie project is comprised of the following Kubernetes objects:

- [Namespace](/Mealie/mealiens.yaml)
- [Deployment](/Mealie/mealie.yaml)
- [Service](/Mealie/service.yaml)
- [Persistent Volume Claim](/Mealie/storage.yaml)

### Notes

- Even though Rancher Desktop offers the possibility of deploying a Service of the LoadBalancer type, I couldn't reach the Mealie application using the External IP. Something to be investigated.

## Homarr

[Homarr](https://homarr.dev/) is the second application we installed in the course. For this project, we used [Helm](https://helm.sh/), a package manager for Kubernetes.

### Installing Homarr from a Helm Chart

```bash
helm repo add oben01 https://oben01.github.io/charts/
helm repo update
helm install homarr oben01/homarr --namespace homarr --create-namespace
```

The third line will tell Helm to install the `homarr` release in the `homarr` Helm chart, that lives in the `oben01` repository, and to do it in the `homarr` namespace. If this namespace doesn't exist yet, it should create it.

## Monitoring

To install the monitoring stack, we also use Helm. We'll use the [Kube Prometheus Stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack).

### Add the Helm repository

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

### Install the Helm Chart

`helm install prometheus-stack prometheus-community/kube-prometheus-stack --namespace=monitoring --create-namespace`

## Linkding

Following up on Mischa's Kubernetes Fundamentals course, I'll try this experiment he suggested. I'll deploy Linkding, a self-hosted bookmark manager.

The original repository: [Linkding](https://github.com/sissbruecker/linkding)

The Linkding project is comprised of the following Kubernetes objects:

- [Namespace](/Linkding/linkdingns.yaml)
- [Deployment](/Linkding/deployment.yaml)
- [Service](/Linkding/linkdingsvc.yaml)
- [Persistent Volume Claim](/Linkding/linkdingstorage.yaml)

![app running on localhost port 9090](/Linkding/Assets/localhost01.png)

### Notes

- For my first deployment, I chose port `6000`. Apparently, this port is considered unsafe by the browser. I changed it to `9090`, like in the original guidelines.
- Before logging in for the first time, you need to setup the username and password. For that, you need to connect to the container and execute a file. This is how I did it:

`kubectl exec -it [linkding-pod] -- python manage.py createsuperuser --username=[username] --email=[email]`

- You'll be prompted for a password.
- In the [Deployment](/Linkding/deployment.yaml), I used `/etc/linkding/data` as the `mountPath`. It works, apparently.
