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

# Kubernetes Resiliency

# Chris Ayers ![w:120](img/portrait.png)

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

## Infrastructure
- Single Points of Failure
- Availability Zones
- Node Pools

---

### Single Points of Failure
- Avoiding single points of failure ensures that your cluster remains operational in case of component failure.
- Distributing workloads across multiple nodes, availability zones, and regions.

---

### Availability Zones
- Group of data centers in a region.
- Distribute workloads across multiple availability zones to ensure high availability.
- Kubernetes can automatically spread workloads across zones.

---

### Node Pools
- Group of nodes with the same configuration.
- Useful for managing and scaling your cluster.
- Can be spread across multiple availability zones for high availability.

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
