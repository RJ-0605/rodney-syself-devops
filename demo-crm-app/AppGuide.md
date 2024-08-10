# Demo App Helm Chart  Guide

## Objective
This guide will walk you through the creation of a Helm Chart to deploy an example application (a placeholder container image) to a Kubernetes cluster. The Helm Chart will include all necessary components (e.g., services, deployments, config maps) and be configurable for a specific namespace.

## Prerequisites
- **Kubernetes cluster** (with `kubectl` access)
- **Helm CLI installed**
- **Basic knowledge of Kubernetes resources** (e.g., Deployments, Services, ConfigMaps)

## Directory Structure
Ensure the Helm Chart follows a proper directory structure:

```bash
    my-app-chart/
    ├── charts/
    ├── templates/
    │   ├── deployment.yaml
    │   ├── service.yaml
    │   ├── configmap.yaml
    │   ├── _helpers.tpl
    │   └── NOTES.txt
    ├── values.yaml
    └── Chart.yaml
```


## Helm  Configuration
- * 1.	Define a Chart.yaml file :
    * Locate the Chart manifest file 
       (e.g., Chart.yaml). see in [file](Chart.yaml)

         ```bash
            ...
                apiVersion: v2
                name: my-app-chart
                description: A Helm chart for deploying a sample application
                version: 0.1.0
            ...
         ```


ii. Create a Values in values.yaml

iii. Create the Deployment Template (templates/deployment.yaml)

iv. Create the Service Template (templates/service.yaml)

v. Create the ConfigMap Template (templates/configmap.yaml)