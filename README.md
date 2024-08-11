# rodney-syself-devops

### **Firstly**

the following were also provided in organized 

i. The  custom built Helm Chart from ground-up code and guide for a [CRM Application](demo-crm-app/AppGuide.md)

ii. The guide to and custom built [MySQL statefulsets](custom-sql-chart/Main_Guide.md) Helm Chart

### **Production Ready Architecture Design**

![1723406983630](image/GuideProductionHelmChart/1723406983630.png)

#### **1. Infrastructure Layer**

* **Virtual Machines (VMs):**
  * **Control Plane Nodes:** A minimum of one VMs to host the control plane components is deployed. However, a minimum of 3 control nodes, ensures high availability and fault tolerance so tht will be used . These nodes will manage the cluster's state, schedule workloads, and maintain consistency.
  * **Worker Nodes:** I will use Multiple VMs to serve as worker nodes. These nodes will run the containerized applications and handle workloads. The number of worker nodes will depend on the expected load and scalability requirements.
* **Private Network:**
  * **Network Segmentation:**  A private network to isolate the Kubernetes environment from external traffic, enhancing security will be implemented. Subnets will be created to separate the control plane and worker nodes, allowing for controlled communication.

#### **2. Operating System Layer**

* **Ubuntu 24.04 LTS:**
  * **Base OS:** All VMs (both control plane and worker nodes) will run Ubuntu 24.04 LTS. This version is chosen for its long-term support, stability, and compatibility with Kubernetes v1.30.3. Regular security updates and patches will be applied to maintain system integrity.

#### **3. Kubernetes Control Plane**

* **Kubernetes Version:** v1.30.3
  * **API Server:** The API server will be deployed on all control plane nodes. It acts as the front-end for the Kubernetes control plane, handling all communication within the cluster.
  * **Etcd:** Etcd will act as a distributed key-value store across all control plane nodes in the architecture. It stores the cluster's state and configuration, ensuring high availability and data consistency.
  * **Controller Manager:** This component will be included for managing the cluster's lifecycle, including node management, replication, and endpoint discovery.
  * **Scheduler:** The scheduler will be deployed on the control plane nodes to assign workloads to the appropriate worker nodes based on resource availability and scheduling policies.

#### **4. Worker Nodes**

* **Kubelet:** Kubelet  is deployed on all worker nodes. It will be responsible for running and managing the containers, ensuring that the desired state defined by the control plane is maintained.
* **Container Runtime:** A container runtime like containerd or CRI-O is used  to manage the lifecycle of containers on the worker nodes. This layer will ensure that containers are launched, monitored, and terminated as needed.
* **Kube-Proxy:** Deploy Kube-Proxy on each worker node to manage network rules and enable communication between services. It ensures seamless networking and load balancing within the cluster.

#### **5. Networking Layer**

* **CNI Plugin:**
  * **Network Plugin:** Container Network Interface (CNI) plugin like Calico or Flannel is installed to manage the networking aspects of the Kubernetes cluster. This plugin will provide features like pod networking, service discovery, and network policies.
  * **Ingress Controller:** An Ingress controller like NGINX or Traefik will be installed to manage external access to the services running in the cluster. This will allow controlled access from outside the private network.

#### **6. Storage Layer**

* **Container Storage Interface (CSI):**
  * **Storage Provisioning:** I will Implement a CSI-compatible storage solution (**OpenEBS CSI Driver**) to manage persistent storage for applications running in the Kubernetes cluster. The CSI will enable dynamic provisioning of storage volumes and ensure that data persists beyond the lifecycle of individual containers.
    *This is how the CSI works as a **volume Driver** and attaches to the node*
    ![1723405073950](image/GuideProductionHelmChart/1723405073950.png)**Persistent Volumes (PVs):** Configure Persistent Volumes and Persistent Volume Claims (PVCs) to allocate storage for stateful applications. These volumes will be backed by the storage solution integrated with the CSI.

#### **7. Security Layer**

* **Authentication and Authorization:**
  * **RBAC:** Role-Based Access Control (RBAC)  is implemented to manage permissions within the cluster. This will ensure that only authorized users and services can perform specific actions. a seperate **VM** can be connected to the cluster for **OIDC** authentication and Auditing of logs.
  * **Network Policies:** Network policies are defined to control traffic flow between pods and services, enhancing the security posture of the environment.
  * **TLS/SSL Certificates:** TLS/SSL certificates to secure communication between components within the Kubernetes cluster between the kube-api server and the following (kubectl, kubelet, kube-scheduler, kube-controller-manager, etcd, kube-proxy, Admission controllers)

#### **8. Monitoring and Logging Layer**

* **Monitoring Tools:**
  * **Prometheus:** Prometheus  was deployed to monitor the health and performance of the Kubernetes cluster. It will collect metrics from the control plane and worker nodes, providing insights into resource utilization and potential issues.
  * **Grafana:** I used Grafana for visualizing the metrics collected by Prometheus, enabling better decision-making and troubleshooting.
* **Logging:**
  * **ELK Stack:** The ELK (Elasticsearch, Logstash, Kibana) stack or a similar logging solution will be implemented  to aggregate and analyze logs generated by the Kubernetes components and applications. This will help in identifying issues and maintaining audit trails.

#### **9. High Availability and Scalability**

* **Load Balancer:**
  * **External Load Balancer:** An external load balancer  is deployed to distribute traffic across the control plane nodes, ensuring high availability and preventing a single point of failure.
* **Auto-Scaling:**
  * **Horizontal Pod Autoscaler (HPA):** HPA  was implemented to automatically scale the number of pods based on resource utilization, ensuring the cluster can handle varying workloads.
  * **Cluster Autoscaler:** Cluster autoscaler will be used to automatically adjust the number of worker nodes in response to workload demands, optimizing resource usage and cost.

#### **10. Backup and Disaster Recovery**

* **Backup Strategy:**
  * **Etcd Backup:**  I will perform regularl back up the Etcd data to ensure that the cluster state can be restored in case of a failure.
  * **Persistent Volume Backups:** Finally i will implement a backup solution for persistent volumes to protect against data loss.
