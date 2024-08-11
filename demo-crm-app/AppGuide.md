# Demo App Helm Chart  Guide

## Objective

The objective  of this  guide is to,  will walk you through the creation of a Helm Chart to deploy my application on a Kubernetes cluster. The Helm Chart will include all necessary components (e.g., services, deployments, config maps) and be configurable for a specific namespace which is prod .

## Prerequisites

- **Kubernetes cluster** (with `kubectl` access)
- The **Namespace** where the  **Services** are going to live
  use the command our `<namespace>` in this case is **prod**
  ```
  kubectl create namespace <namespace-name>
  ```
- **Helm CLI installed**
- **Basic knowledge of Kubernetes resources** (e.g., Deployments, Services, ConfigMaps)

## Directory Structure

Demo CRM the Helm Chart follows a proper directory structure:

```bash
    demo-crm-app/
    ├── charts/
    ├── templates/
    │   ├── deployment.yaml
    │   ├── service.yaml
    │   ├── secret.yaml
    │   ├── configmap.yaml
    │   ├── ingress.yaml
    │   ├── hpa.yaml
    │   └── cert-manager.yaml
    ├── values.yaml
    └── Chart.yaml
```

## Helm  Configuration

- * 1. A  Chart.yaml file, was defined
       a. Added the dependencies such as an database helm chart if it will be needed,
       b. also an ingress controller for a way to enable traffic from the user to the app services
       c. and cert-manager helm chart for TLS termination

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
- * 2. Next  i  created  a Values in values.yaml to store values.
       This makes maintenance of the Chart very easy and reusable, also makes it Scalable by **using Go template syntax**:

    * For values file see in [file](values.yaml)

      ```bash
         ...
                app:
                name: demo-crm-app
                spec:
                    replicas: 2
                imageBlue: "gcr.io/my-first-kuba/nextjs-docker:3.0"
                namespace: prod

                hpa:
                    minReplicas: 2 
                    maxReplicas: 10 
                    targetCPUUtilizationPercentage: 80 
                    targetMemoryUtilizationPercentage: 80 
         ...
      ```
- * 3. I created a Deployment Template (deployment.yaml) [file](templates/deployment.yaml).

       ```bash
          ...
              apiVersion: apps/v1
              kind: Deployment
              metadata:
              name: {{ .Values.app.name }}-deployment
              namespace: {{ .Values.namespace }}
              labels:
                  app: {{ .Values.app.name }}
          ...
       ```
       NB:
       i. Requests and limits were included to ensure maximum resources in terms of CPU and Memory were delegated to the Deployments, which is very needful in a production environment
       ii. Readiness and liveness probe were used in collaboration with specific  REST API endpoint in the app design, to ensure that a container is healthy and ready to serve traffic
- * 4. I also added the Service Template (service.yaml) [file](templates/service.yaml)

       ```bash
          ...
                apiVersion: v1
                kind: Service
                metadata:
                name: {{ .Values.app.name }}-service
                namespace: default
                spec: 
                type: ClusterIP
                selector:
                app: {{ .Values.app.name }}
                ports:
                - protocol: TCP
                    port: 80
                    targetPort: 3000


          ...
       ```
- * 5. Next thing i added was Ingress Template templates/ingress.yaml see [file](templates/ingress.yaml)

    ```bash
       ...
           namespace: default
           app:
               name: my-app
               image: "nginx:latest"
               replicas: 2
               config:
       ...
    ```
- * 6. I created the ConfigMap Template templates/configmap.yaml see [file](templates/configmap.yaml)
- * 7. I added the Secret Template [file](templates/secret.yaml)
- * 8. Also i attached the Horizontal Pod AutoScaler automatically updates a workload resource (such as a Deployment or StatefulSet), with the aim of automatically scaling the workload to match demand. [file](templates/hpa.yaml)
- * 9. Finally  i attached  the cert-manager template  to enable TLS termination for the ingress [file](templates/cert-manager.yaml)
    10. I will Run the Helm Chart to deploy the app
        ```
        helm install demo-crm-app-release ./demo-crm-app \
          --namespace prod \
          --values ./values.yaml
        ```
  * Finally Monitor the deployment using Kubernetes tools and ensure the demo-crm-app is functioning correctly.
    with helm

    ```
    helm status demo-crm-app-release --namespace prod

    ```
    and  with *kubectl*

    ```
    kubectl get pods --namespace prod -l app=<app-label>

    ```
