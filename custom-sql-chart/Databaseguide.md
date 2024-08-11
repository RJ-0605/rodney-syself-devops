Database Guide: Choosing MySQL with Helm Charts

- * 1. Introduction
       This guide outlines the rationale for choosing MySQL as the database for my  CRM application, i seek to detail the implementation plan using custom built MYSQL  Helm charts, and explains why i decided to customize my own Helm chart. However i have more details of helm Chart  in
- * 2. Why MySQL?
       MySQL is a well-established, open-source relational database management system (RDBMS) known for its reliability, scalability, and ease of use. Here’s why MySQL is an excellent choice for the CRM application:
       a. **Relational Data Structure**: The CRM application requires structured data storage with complex relationships between entities, making a relational database like MySQL ideal.
       b. **ACID Compliance**: MySQL ensures data integrity through ACID (Atomicity, Consistency, Isolation, Durability) compliance, which is crucial for the transactional nature of CRM systems.
       c. **Scalability**: MySQL offers robust scalability options, enabling the CRM application to handle growth in data volume and user load.
       d. **Active Community and Support**: MySQL's large user base and active development community provide access to a wealth of resources, updates, and security patches.
- 3. Custom SQl Helm CHart
     I must add that i built this custom SQL helm chart from ground up with the tradtional SQL manifests which can be found in the Kubernetes Documentation.
- 4. StatefulSets for MySQL
     I chose to deploy MySQL using StatefulSets for the following reasons:

     a. Stable Network Identities: Each pod in a StatefulSet has a unique, stable network identifier, which is crucial for MySQL replication.
     b. Persistent Storage: StatefulSets work well with persistent volumes, ensuring that data remains intact even when pods are rescheduled.
     c. Ordered Deployment & Scaling: StatefulSets ensure that MySQL pods are deployed and scaled in an orderly manner, maintaining data consistency.
- 5. Implementation Plan
     I Created a custom Helm chart to manage the deployment of MySQL with the following components.
     * StatefulSet: This is used to manage the deployment and scaling of MySQL instances.
     * Services: Includes both a headless service for internal communication between MySQL pods and a client service for external access.
     * ConfigMap & Secrets: Manages configuration settings and sensitive data like passwords.
- 7. Custom Helm Chart: I opted to create a custom Helm chart instead of relying entirely on existing charts for the following reasons:

  * Customization: I needed specific configurations tailored to my application's needs, such as custom replication settings and security enhancements.
  * Control & Flexibility: Customizing the Helm chart allowed us to maintain full control over the deployment, ensuring it aligns with my production-readiness criteria.
  * Integration: My custom chart is designed to integrate seamlessly with other components of my application, which are managed in a **unified Helm chart**.
- 8. Deployment Steps:

  * My Next point is to Configure my Kubernetes cluster and ensure persistent storage is available, and also configure a Storage Class for my PVC if its not already done for a **local-path provisioner** using **Rancher** ,I could also do for NFS or block storage if those options are available with the use of a **CSI Driver**
  * I will Deploy the MySQL StatefulSet using the custom Helm chart.
    with these

    ```
    helm install mysqlDB-release ./custom-sql-chart \
      --namespace prod \
      --values ./values.yaml
    ```
  * Finally Monitor the deployment using Kubernetes tools and ensure the database is functioning correctly.
    with helm

    ```
    helm status mysqlDB-release --namespace prod

    ```
    and  with *kubectl*

    ```
    kubectl get pods --namespace prod -l app=<app-label>

    ```
