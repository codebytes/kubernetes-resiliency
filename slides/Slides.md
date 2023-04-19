---
marp: true
theme: default
footer: 'https://chris-ayers.com'
style: |
  .columns {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 1rem;
  }
  .columns3 {
    display: grid;
    grid-template-columns: repeat(3, minmax(0, 1fr));
    gap: 1rem;
  } 
  img[alt~="center"] {
    display: block;
    margin: 0 auto;
  }
  .fa-twitter { color: aqua; }
  .fa-mastodon { color: purple; }
  .fa-linkedin { color: blue; }
  .fa-window-maximize { color: skyblue; }

  svg[id^="mermaid-"] { 
    min-width: 480px; 
    max-width: 960px; 
    min-height: 360px; 
    max-height: 600px; 
  }

  @import 'https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.3.0/css/all.min.css'
---

<!-- _footer: 'https://github.com/Codebytes/kubernetes-resiliency' -->

![bg left w:500px](./img/Kubernetes_Logo.png)

# <!--fit--> Kubernetes Resiliency

## Chris Ayers ![w:120](img/portrait.png)

---

![bg left:40%](./img/portrait.png)

## Chris Ayers
### Senior Customer Engineer<br>Microsoft

<i class="fa-brands fa-twitter"></i> Twitter: @Chris\_L\_Ayers
<i class="fa-brands fa-mastodon"></i> Mastodon: @Chrisayers@hachyderm.io
<i class="fa-brands fa-linkedin"></i> LinkedIn: - [chris\-l\-ayers](https://linkedin.com/in/chris-l-ayers/)
<i class="fa fa-window-maximize"></i> Blog: [https://chris-ayers\.com/](https://chris-ayers.com/)
<i class="fa-brands fa-github"></i> GitHub: [Codebytes](https://github.com/codebytes)

---

# Agenda

- Infrastructure
- Components and Services
- Workloads and Applications

---

## Infrastructure

---

## Infrastructure
- Single Points of Failure
- Availability Zones
- Node Pools

---

### Single Points of Failure
- Avoiding single points of failure ensures that your cluster remains operational in case of component failure.
- Distributing workloads across multiple nodes, availability zones, and regions.
- Use replication controllers or replica sets to maintain multiple replicas of your applications.
- Configure your load balancer to distribute traffic across multiple nodes.
- Employ a multi-master setup for etcd and the Kubernetes control plane components.

---

### Availability Zones
- Group of data centers in a region.
- Distribute workloads across multiple availability zones to ensure high availability.
- Kubernetes can automatically spread workloads across zones using the topologySpreadConstraints feature.
- Use Regional Persistent Volumes (PVs) for storage that is available across multiple zones.
- Leverage Kubernetes Federation to manage clusters across multiple regions.

---

### Node Pools
- Group of nodes with the same configuration.
- Useful for managing and scaling your cluster.
- Can be spread across multiple availability zones for high availability.
- Allows for heterogeneous cluster configurations, such as using different instance types or hardware.
- Simplifies cluster management by enabling separate upgrade and scaling processes for different node types.
- Use Kubernetes taints and tolerations to separate workloads on specific node pools.

---

### Infrastructure Best Practices
- Regularly review and update your infrastructure to adapt to changing requirements.
- Monitor and analyze the performance of your nodes, availability zones, and node pools.
- Use auto-scaling groups to adjust the size of your node pools based on demand.
- Implement infrastructure as code (IaC) tools like Terraform to manage and version your infrastructure configuration.
- Establish a disaster recovery plan that includes backup and restoration processes for your cluster data and applications.

---

![bg right fit](img/components-of-kubernetes.svg)
## Kubernetes Components Overview

- Control Plane Components
  - kube-apiserver
  - etcd
  - kube-scheduler
  - kube-controller-manager
  - cloud-controller-manager

---

![bg right fit](img/components-of-kubernetes.svg)
## Kubernetes Components Overview

- Node Components
  - kubelet
  - kube-proxy
  - Container runtime

---

### kube-apiserver High Availability

- Deploy multiple instances of kube-apiserver.
- Use a load balancer to distribute traffic among instances.
- Store the backing data in a highly available etcd cluster.
- Regularly back up your etcd data.

---

### etcd High Availability

- Deploy etcd as a multi-node cluster.
- Configure each etcd node with the `--initial-cluster-state` flag for an existing cluster.
- Use an odd number of etcd nodes (e.g., 3, 5, 7) to maintain quorum.
- Regularly back up etcd data and have a restore strategy in place.

---

### kube-scheduler High Availability

- Run multiple instances of kube-scheduler.
- Use leader election to ensure only one active scheduler is making decisions at a time.
- Configure the `--leader-elect` flag for each kube-scheduler instance.

---

### kube-controller-manager High Availability

- Run multiple instances of kube-controller-manager.
- Use leader election to ensure only one active controller-manager is running controllers at a time.
- Configure the `--leader-elect` flag for each kube-controller-manager instance.

---

### cloud-controller-manager High Availability

- Run multiple instances of cloud-controller-manager.
- Use leader election to ensure only one active cloud-controller-manager is running controllers at a time.
- Configure the `--leader-elect` flag for each cloud-controller-manager instance.

---

### kubelet High Availability

- Ensure each node runs kubelet.
- Monitor kubelet processes and restart them if they fail.
- Use a Kubernetes cluster autoscaler to adjust the number of nodes based on demand.

---

### kube-proxy High Availability

- Run kube-proxy on each node in the cluster.
- Use a DaemonSet to automatically deploy and manage kube-proxy instances.
- Monitor kube-proxy processes and restart them if they fail.

---

### Container Runtime High Availability

- Choose a container runtime that supports high availability, such as containerd or CRI-O.
- Monitor the container runtime on each node and restart it if it fails.
- Use Kubernetes features like PodDisruptionBudgets and ReplicaSets to ensure application availability.

---

### Addons High Availability

- Deploy addons as highly available components using ReplicaSets, Deployments, or StatefulSets.
- Configure multiple replicas for each addon to ensure fault tolerance.
- Monitor and manage addon components to maintain their availability and performance.

---

## Conclusion

- Ensure high availability for all Kubernetes components.
- Monitor and manage components to maintain resiliency.
- Use addons to provide additional functionality and enhance the Kubernetes cluster.

---

## Components
- etcd
- API Server
- kube-scheduler

---

### etcd
- Distributed key-value store.
- Stores the configuration data of the cluster.
- Ensures consistency and high availability through consensus.

---

### API Server
- Exposes the Kubernetes API.
- Accepts and processes RESTful operations on cluster resources.
- Can be scaled horizontally for high availability.

---

### kube-scheduler
- Assigns newly created pods to nodes.
- Considers resource availability and constraints when making decisions.
- Can be run in an active-passive configuration for high availability.


---

## Azure Kubernetes Service (AKS) High Availability

- Control Plane High Availability
- etcd High Availability
- Node High Availability
- Load Balancer Integration
- Auto-Scaling
- Upgrade and Patching
- Monitoring and Logging
- Backup and Disaster Recovery
- Identity and Access Management

---

### Control Plane High Availability

- AKS offers a highly available control plane by default.
- Runs multiple instances of kube-apiserver, kube-scheduler, kube-controller-manager, and other components.

---

### etcd High Availability

- AKS manages the etcd cluster for you.
- Ensures that it is highly available, distributed, and automatically backed up.

---

### Node High Availability

- Configure multiple node pools spanning across multiple availability zones.
- Distribute workloads and achieve high availability for worker nodes.

---

### Load Balancer Integration

- Automatically integrates with Azure Load Balancer and Azure Application Gateway.
- Distributes traffic to nodes and ensures high availability for applications.

---

### Auto-Scaling

- Supports Kubernetes Cluster Autoscaler.
- Automatically adjusts the number of nodes in the cluster based on workload demand.

---

### Upgrade and Patching

- Automates applying security patches and upgrading Kubernetes versions.
- Ensures the cluster is always up to date and highly available.

---

### Monitoring and Logging

- Integrates with Azure Monitor, Log Analytics, and other Azure services.
- Monitors health and performance of Kubernetes cluster components and applications.

---

### Backup and Disaster Recovery

- Offers automatic backup and restore capabilities for control plane and etcd components.
- Use Azure services like Azure Backup and Azure Site Recovery for additional backup and recovery options.

---

### Identity and Access Management

- Integrates with Azure Active Directory (AAD).
- Ensures secure access and role-based access control to the Kubernetes cluster and its resources.

---

## Applications
- Pod Disruption Budgets
- Resource Requests and Limits
- Liveness and Readiness Probes

---

### Pod Disruption Budgets
- Defines the minimum number of pods that must be available.
- Ensures high availability during voluntary disruptions like node maintenance.
- Can be specified per application or for the whole cluster.

---

### Resource Requests and Limits
- Requests: minimum resources required by a container.
- Limits: maximum resources a container can use.
- Helps maintain stability by preventing resource contention.

---

### Why Use Requests Over Limits?
- Requests ensure minimum resources for a container.
- Kubernetes uses requests for scheduling decisions.
  - Ensures containers have enough resources to run.
  - Prevents resource starvation for critical workloads.
- Limits cap resource usage to prevent overconsumption.
  - May cause containers to be terminated if exceeded.
  - Should be used as a safety net, not for scheduling purposes.
- Focusing on requests optimizes cluster resource utilization and stability.

---

### Liveness Probes
- Checks if a container is running.
- If the probe fails, Kubernetes restarts the container.
- Helps ensure that failed containers are automatically recovered.

---

### Readiness Probes
- Checks if a container is ready to serve traffic.
- If the probe fails, Kubernetes stops sending traffic to the container.
- Ensures that only healthy containers receive traffic.

---

# Conclusion
- Kubernetes provides several features to ensure resiliency.
- Optimize your infrastructure and applications for high availability and stability.
- Continuously monitor and adjust your cluster to maintain resiliency.

---

# Demos

---

# Questions

![bg right](./img/owl.png)

---

# Resources 

<div class="columns">
<div>

## Links

- [https://docs.microsoft.com/en-us/events/learntv/learnlive-iac-and-bicep/](https://docs.microsoft.com/en-us/events/learntv/learnlive-iac-and-bicep/)
- [https://github.com/codebytes](https://github.com/codebytes)

</div>
<div>

## Chris Ayers 

<i class="fa-brands fa-twitter"></i> Twitter: @Chris\_L\_Ayers
<i class="fa-brands fa-mastodon"></i> Mastodon: @Chrisayers@hachyderm.io
<i class="fa-brands fa-linkedin"></i> LinkedIn: - [chris\-l\-ayers](https://linkedin.com/in/chris-l-ayers/)
<i class="fa fa-window-maximize"></i> Blog: [https://chris-ayers\.com/](https://chris-ayers.com/)
<i class="fa-brands fa-github"></i> GitHub: [Codebytes](https://github.com/codebytes)

</div>
</div>
