---
marp: true
theme: custom-default
transition: cover
footer: 'https://chris-ayers.com'
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
<i class="fa-brands fa-linkedin"></i> LinkedIn: [chris-l-ayers](https://linkedin.com/in/chris-l-ayers/)
<i class="fa fa-window-maximize"></i> Blog: [https://chris-ayers.com/](https://chris-ayers.com/)
<i class="fa-brands fa-github"></i> GitHub: [Codebytes](https://github.com/codebytes)

---

<div class="columns">
<div>

# Agenda

- Infrastructure
- Kubernetes Components
- Networking
- Applications & Services
- Testing

</div>
<div>

<video width="100%" autoplay loop muted>
  <source src="./img/cargo-ship.mp4" type="video/mp4" />
  Your browser does not support the video tag.
</video>

</div>
</div>

---

![bg right:60% w:700](img/kubernetes-infra-highlevel.drawio.png)

## Infrastructure

- Single Points of Failure
- Availability Zones
- Node Pools

---

### Single Points of Failure / Failure Domains


- **Failure Domains**: Areas where localized failures occur.
- Avoid single points of failure to maintain operational cluster resilience.
  - Compute (Nodes)
  - Storage (PV)
  - Networking (LB)
- Distribute workloads across multiple nodes, availability zones, and regions.
- Manage clusters across multiple regions using Kubernetes Federation.

---

### Planning for Failure Domains
- **Multi-zonal and Multi-regional Deployments**
- **Topology Awareness**
- **Replica Distribution**
![bg right:40% w:70%](img/spread.png)

---

### Availability Zones

- Use Zone Aware services for high availability.
  - Storage
  - Load Balancers
  - Registries
- Utilize Kubernetes topologySpreadConstraints feature to control scheduling

![bg right 140%](img/availability-zones.png)

---

### Kubernetes TopologySpreadConstraints

<div class="columns">
<div>


Utilize Kubernetes' topologySpreadConstraints feature to automatically spread workloads.

- Provides **fine-grained control over Pod placement** across different topologies
- Helps distribute Pods **evenly across Nodes, Zones, Racks, or Regions**
- User defines `maxSkew` to limit **Pod count disparity** across Nodes

</div>
<div>

<br />
<br />
<br />
<br />

<div class="mermaid">
graph BT
    subgraph "zoneB"
        direction BT
        p3(Pod) --> n3(Node3)
        n4(Node4)
    end
    subgraph "zoneA"
        direction BT
        p1(Pod) --> n1(Node1)
        p2(Pod) --> n2(Node2)
    end
  classDef plain fill:#ddd,stroke:#fff,stroke-width:4px,color:#000;
  classDef k8s fill:#326ce5,stroke:#fff,stroke-width:4px,color:#fff;
  classDef cluster fill:#fff,stroke:#bbb,stroke-width:2px,color:#326ce5;
  class n1,n2,n3,n4,p1,p2,p3 k8s;
  class zoneA,zoneB cluster;

</div>

</div>
</div>

---

### Node Pools

- Group nodes with the same configuration.
- Enable effective cluster management and scalability.
- Spread node pools across multiple availability zones for high availability.
- Support heterogeneous cluster configurations with different instance types or hardware.
- Simplify cluster management with separate upgrade and scaling processes for different node types.
- Use Kubernetes taints and tolerations to segregate workloads on specific node pools.

---

### System vs User Node Pools
<br />
<br />
<br />
<br />
<br />
<br />
<br />
<br />


![bg 90%](./img/nodepool.drawio.png)

---

# Kubernetes Federation / Fleet Management

<div class="columns">
<div>

- **Federation**
- **Fleet Management**

</div>
<div>

- **Unified Management**
- **Cross-Cluster Scheduling**
- **Resource Sharing**

</div>
</div>


---

### Infrastructure Best Practices

- Regularly review and update infrastructure to meet changing requirements.
- Monitor and analyze node, availability zone, and node pool performance.
- Use auto-scaling groups to adjust node pool sizes based on demand.
- Implement infrastructure as code (IaC) tools like Bicep or Terraform for infrastructure configuration management.
- Establish a disaster recovery plan, including backup and restoration processes for cluster data and applications.

---

## Kubernetes Components ![bg right:60% w:750](img/kubernetes-infra.drawio.png)


---

![bg right:60% w:750](img/kubernetes-infra-control.drawio.png)
## Kubernetes Control Plane Components Overview

- kube-apiserver
- etcd
- kube-scheduler
- kube-controller-manager
- cloud-controller-manager

---

![bg right:60% w:750](img/kubernetes-infra-node.drawio.png)
## Kubernetes Node Components Overview

- kubelet
- kube-proxy
- Container runtime

---

### High Availability (HA) in Kubernetes

<div class="columns">
<div>

- Deploying multiple instances of components
  -  Using leader election for coordination
  -  Regular backup of important data. 
- Each component requires monitoring
- A strategy for automatic restart upon failure. 

</div>
<div>

- **Control Plane Components**: kube-apiserver, etcd, kube-scheduler, kube-controller-manager, and cloud-controller-manager.
- **Node Components**: kubelet and kube-proxy.
- **Container Runtime**: Choose a runtime that supports HA, such as containerd or CRI-O.

</div>
</div>  

---

### Additional High Availability Considerations

- **Addons**: Deploy as highly available components using ReplicaSets, Deployments, or StatefulSets and ensure fault tolerance.
- **Avoid naked pods!**

![bg right](./img/shipping-is-not-risk-free.gif)

---

# Networking

![bg left fit](./img/networking.jpg)

---

### Rate Limiting

![bg right fit 95%](img/rate-limiting.drawio.png)

Rate limiting is a technique for limiting network traffic. It sets a limit on how many requests a client can make to a server in a given amount of time. 

- Protects your services from being overwhelmed by too much traffic.
- Can be implemented at the application level or using a proxy server.

---

### Circuit Breaking


![bg right:40% fit 35%](img/circuit-breaker.drawio.png)


Circuit breaking is a technique used to prevent an application from trying to perform an operation that's likely to fail, allowing it to continue to operate without waiting for the fault or timeout to be fixed.

- Prevents application failure from cascading to other services.
- Allows applications to fail fast and recover rapidly.

---

### Service Meshes

A **service mesh** is a dedicated infrastructure layer designed to facilitate service-to-service communications between microservices, typically using a sidecar proxy.

- **Improved Traffic Management**: Load balancing, rate limiting, circuit breaking.
- **Enhanced Observability**: Insights, metrics, logs, traces.
- **Robust Security**: Mutual TLS, service-to-service communications.
- **Simplified Service Discovery**: Dynamic service discovery.

---

## Applications

- Deployment Patterns
- Horizontal Pod Autoscaler
- Kubernetes Event-driven Autoscaling (KEDA)
- Pod Disruption Budgets
- Resource Requests and Limits
- Liveness and Readiness Probes

![bg left](img/cargo-ship.jpg)

---


# Blast Radius of Change

![bg right fit](img/blast-radius.png)

- The "Blast Radius" refers to the potential impact of a change or failure.
- Understanding and managing the blast radius is crucial in minimizing disruptions.

---

# Feature Flags & Toggles

- Enable/Disable features without deploying new code.
- Gradually roll out features to a subset of users.
- Easily roll back problematic features.
- Test in production, ensuring real-world performance.

---

# Canary Deployments

![bg right w:55%](img/canary1.drawio.png)

A release strategy involving gradual rollout to a small group before full deployment.

---

# Canary Deployments

![bg right w:55%](img/canary2.drawio.png)

"Canaries" are the initial users/servers to detect issues or bugs in the new release.

---

# Canary Deployments

![bg right w:55%](img/canary3.drawio.png)

Incremental deployment reduces the risk of widespread outages.

---

# Canary Deployments

![bg right w:55%](img/canary4.drawio.png)

Continuous monitoring and feedback gather performance insights.

---

# Blue Green Deployments

![bg right:40% w:90%](img/blue-green1.drawio.png)

- Parallel Environments: Maintain Blue (production) and Green (updates).
- Zero Downtime: Switch between Blue and Green without downtime.
- Testing & Validation: Thoroughly validate updates in the Green environment.
- Instant Rollback: Quickly revert to the stable Blue environment if issues arise.

---

# Blue Green Deployments

![bg right:40% w:90%](img/blue-green2.drawio.png)

- Controlled Release: Gradually shift traffic from Blue to Green.
- Canary Testing: Test updates with a small portion of traffic in the Green environment.
- Traffic Cut-Over: Switch all traffic to Green after successful validation.
- Rollback Option: Instantly return to the stable Blue environment if needed.

---

<div class="columns">
<div>

### Metrics Server

<br />

The resource metrics pipeline provides a limited set of metrics related to cluster components such as the Horizontal Pod Autoscaler controller, as well as the kubectl top utility. These metrics are collected by the lightweight, short-term, in-memory metrics-server and are exposed via the metrics.k8s.io API.

</div>
<div>

<div class="mermaid">
flowchart BT
subgraph cluster[Cluster]
direction RL
S[ <br><br> ]
A[Metrics-<br>Server]
subgraph B[Nodes]
direction TB
D[cAdvisor] --> C[kubelet]
E[Container<br>runtime] --> D
E1[Container<br>runtime] --> D
P[pod data] -.- C
end
L[API<br>server]
W[HPA]
C ---->|Summary<br>API| A -->|metrics<br>API| L --> W
end
L ---> K[kubectl<br>top]
classDef box fill:#fff,stroke:#000,stroke-width:1px,color:#000;
class W,B,P,K,cluster,D,E,E1 box
classDef spacewhite fill:#ffffff,stroke:#fff,stroke-width:0px,color:#000
class S spacewhite
classDef k8s fill:#326ce5,stroke:#fff,stroke-width:1px,color:#fff;
class A,L,C k8s
</div>

</div>
</div>

---

### Horizontal Pod Autoscaler


<div class="columns">
<div>
<br/ >

- **Handling Traffic Spikes**
- **Efficient Resource Usage** 
- **Custom Metrics Scaling**
- **Stability During Failures**

> The Horizontal Pod Autoscaler (HPA) in Kubernetes enhances application resilience by dynamically adjusting the number of pod replicas based on observed performance metrics.

</div>
<div>

<br />
<br />

<div class="mermaid">
graph BT
hpa[Horizontal Pod Autoscaler] --> scale[Scale]
subgraph rc [RC / RS / Deployment]
scale
end
scale -.-> pod1[Pod 1]
scale -.-> pod2[Pod 2]
scale -.-> pod3[Pod N]
classDef hpa fill:#D5A6BD,stroke:#1E1E1D,stroke-width:1px,color:#1E1E1D;
classDef rc fill:#F9CB9C,stroke:#1E1E1D,stroke-width:1px,color:#1E1E1D;
classDef scale fill:#B6D7A8,stroke:#1E1E1D,stroke-width:1px,color:#1E1E1D;
classDef pod fill:#9FC5E8,stroke:#1E1E1D,stroke-width:1px,color:#1E1E1D;
class hpa hpa;
class rc rc;
class scale scale;
class pod1,pod2,pod3 pod
</div>

</div>
</div>

---


### Kubernetes Event-driven Autoscaling (KEDA)

- **Event-Driven Scaling**: Scale based on events.
- **Various Event Sources**: Kafka, RabbitMQ, Azure Service Bus.
- **Scaling to Zero**: Save resources when no events.
- **Works With Kubernetes HPA**: Extend HPA for external metrics.

---

### Pod Disruption Budgets (PDB)

- **Pod Disruption**: Impacts the running state of a Pod, making it unavailable. 
    - Involuntary: Unplanned scenarios like hardware failures. 
    - Voluntary: Planned actions like manual upgrades.
- **Pod Disruption Budget (PDB)**: Sets rules for the number of disruptions an application can handle. 

#### PDB Definitions
> **Minimum Available**:  Minimum number of replicas of a Pod that must be available.
> **Maximum Unavailable**: Maximum number of replicas that can be unavailable.

<!--

## PDB Pitfalls
1. May block or delay certain operations like node drain or Kubernetes version upgrades.
2. Could impact the ability to scale down the cluster.

 ## PDB Use-cases
1. Ensuring a single instance app remains running during autoscaling.
2. Keeping highly available apps running during operations that would otherwise disrupt all replicas.
3. Enforcing rolling updates to ensure continuous availability.

Note: PDB won't prevent direct removal of Pods, total node removal, but it can help manage disruptions due to node memory pressure and node pool upgrades.

 -->

---

### Resource Requests

- **What are Resource Requests?** They define the minimum resources required by a container.
- **Resource Types:** Specify CPU and memory needs for optimal performance.
- **Scheduling Decisions:** Kubernetes uses resource requests to make informed scheduling decisions.

---

### Importance of Resource Requests

- **Optimal Performance:** Resource requests ensure your container has the necessary resources for optimal performance.
- **Balanced Scheduling:** Inform Kubernetes on how to balance resources across the cluster through defined resource requests.
- **Avoid Resource Starvation:** Resource requests prevent a container from being starved of resources by other more demanding containers.

---

### Resource Limits

- **What are Resource Limits?** They define the maximum resources a container can use.
- **Safety Guard:** Limits act as a safety guard against resource overuse, ensuring fair usage across all containers.
- **Exceeding a Limit:** When a container exceeds the set limit, it can be terminated or throttled.

---

### Importance of Resource Limits

- **Avoid Overconsumption:** Resource limits prevent a container from consuming all resources on a node.
- **Stability of Workloads:** By setting limits, you ensure the stability of other workloads on the same node.
- **Prevent Disruptions:** Resource limits help avoid disruptions if a container starts consuming more resources than expected.

---

### Dangers of Misconfigured Resource Limits

- **Unexpected termination:** If a container exceeds its limit, it can be killed unexpectedly, leading to service disruption.
- **Performance degradation:** If limits are too restrictive, the container might be constantly throttled, resulting in degraded performance.
- **Resource waste:** Setting limits too high can lead to resource wastage, as unused resources are reserved and can't be used by other containers.

---

![bg fit](img/limit.drawio.png)

---

### Quality of Service (QoS) Classes 

<div class="columns">
<div>

- **Guaranteed**
  - Containers have equal memory/CPU limits and requests
- **Burstable**
  - At least one Container has a memory or CPU request or limit
- **BestEffort**
  - No Containers have a memory/CPU limit or request

</div>
<div>

- **QoS determines Pod eviction order** under Node pressure: 
  - BestEffort, Burstable, Guaranteed
- **Memory QoS with cgroup v2 (alpha)**
  - Uses cgroup v2 for guaranteed memory resources
  - Works in conjunction with, but separate from, QoS class assignments

</div>
</div>

---

**PriorityClass**
A non-namespaced object used to define the relative priority of Pods.

- Assign priorities to Pods, ensuring critical workloads are scheduled first.
- Control Pod eviction order during resource contention.

  - **Resource Assurance**: Ensure critical Pods get the resources they need.
  - **Controlled Eviction**: Manage Pod eviction in a controlled manner during resource crunches.

---

<div class="columns">
  <div>

  ### Liveness Probes

  - Check if a container is running.
  - Restart the container if the probe fails.
  - Ensure failed containers are automatically recovered.

  </div>
  <div>
  
  ### Readiness Probes

  - Check if a container is ready to serve traffic.
  - Stop sending traffic to the container if the probe fails.
  - Ensure only healthy containers receive traffic.

  </div>
</div>

---

# Testing
![bg right:70% fit](./img/i-dont-always-test-but-when-i-do-i-test-in-production.jpg)

---

![bg 80%](./img/test-in-production-meme.jpg)

---

## Load Testing Your Cluster

Load testing is the practice of testing system performance under normal and peak usage conditions. It helps identify bottlenecks, performance degradation, and capacity limits in your Kubernetes cluster.

- Use load testing tools (like Azure Load Testing, Jmeter, Locust, Gatling, Siege, etc.)
- Test your services under normal and extreme conditions.
- Monitor performance metrics (CPU, Memory, Network IO).
- Improve scalability & resilience based on findings.

---

## Chaos Testing

Chaos Testing, or Chaos Engineering, is the discipline of experimenting on a system to uncover its weaknesses.

- Introduce failures into your Kubernetes cluster deliberately (like pod failures, network failures, etc.) using tools like Azure Chaos Studio, Litmus, Chaos Mesh, PowerfulSeal.
- Test the system's ability to maintain availability and minimize impact.
- Helps in building robust, fault-tolerant systems by proactively identifying and fixing potential issues.

---

# Conclusion

- Kubernetes excels at providing resiliency, ideal for managing complex, distributed systems.
- Optimized infrastructure, regular monitoring, and adaptability are key to ensuring high availability and stability.
- High availability of Kubernetes components and applications is crucial for system resilience.
- Kubernetes ensures application availability via mechanisms such as Pod Disruption Budgets, Resource Requests, Limits, and Probes.
- Continuous improvement and adaptability to change are critical to maintaining resiliency and performance.

---

# Questions

![bg right](./img/owl.png)

---

# Resources 

<div class="columns">
<div>

## Links

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Kubernetes Production Best Practices](https://learnk8s.io/production-best-practices)
- [https://github.com/codebytes](https://github.com/codebytes)

</div>
<div>

## Chris Ayers 

<i class="fa-brands fa-twitter"></i> Twitter: @Chris\_L\_Ayers
<i class="fa-brands fa-mastodon"></i> Mastodon: @Chrisayers@hachyderm.io
<i class="fa-brands fa-linkedin"></i> LinkedIn: [chris-l-ayers](https://linkedin.com/in/chris-l-ayers/)
<i class="fa fa-window-maximize"></i> Blog: [https://chris-ayers.com/](https://chris-ayers.com/)
<i class="fa-brands fa-github"></i> GitHub: [Codebytes](https://github.com/codebytes)

</div>
</div>

<!-- Needed for mermaid, can be anywhere in file except frontmatter -->
<script type="module">
  import mermaid from 'https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs';
  mermaid.initialize({ startOnLoad: true });
</script>