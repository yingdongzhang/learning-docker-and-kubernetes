# Helm notes

Helm is a package manager for Kubernetes, similar to Homebrew/Apt/Yum/npm etc.

Packages YAML files and distributes them in public and private repositories.

## Advantages
* Deploy crazy microservice architectures
* Quick app portability
* Better testing
* Easy dev onboarding
* Rollbacks
* Template engine allows reusability and better CI & CD process
* Because `kubectl apply -f` is dumb?

## Chart

A packaged k8s resource.

* Bundle of YAML files.
* Create your own Helm Charts with Helm.
* Push them to Helm repository.
* Download and use existing ones.

There's chart of charts.

## Chart Repository

Just like Dockerhub, but for Charts

## Release Management

A deployed instance of a chart.

Installation of the same chart is a new release.

### v2 vs. v3
Helm Version 2 comes in two parts:
* Client (helm CLI)
* Server (Tiller)

Tiller has too much power inside of k8s cluster

In v3 Tiller got removed
- Solves the security conern, but makes it more difficult to use

## Helm Chart Structure
mychart
    |-Chart.yaml
    |-values.yaml
    |-charts/
    |-templates/
    ...

* Chart.yaml: meta info about chart
* values.yaml: values for the template files. Can have default values.
* charts: chart dependencies
* templates: the actual template files

Template files will be filled with the values in values.yaml
