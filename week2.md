# Week2

## Configuring Networking in Kubernetes

Introduce communication between Kubernetes applications and the rest of the world.

**Learning Objectives**

- Enable intra-pod network communications for applications deployed in Kubernetes, and learn how to keep communication up even with automatic deployments.
Expose service-backed applications to clients outside the Kubernetes cluster.

## Exposing Application for External Access

### Kubernetes Networking

**Summary:**

This section focuses on networking within Kubernetes. While previous videos covered deploying managed pods in Kubernetes, this session emphasizes interacting with those deployed workloads. 

**Key Points:**
- Pods in Kubernetes are assigned their own IP addresses.
- A pod can host multiple containers, which communicate using localhost and share a network space.
- While containers within the same pod share network space, pods themselves are isolated and each gets a unique IP address.
- Within a Kubernetes cluster, any pod can communicate with any other pod using their respective IP addresses.
- Kubernetes clusters typically consist of multiple nodes (e.g., 3, 5, 7 nodes), and pods can send requests to other pods using their IPs.


### Introducing Kubernetes Services

**Summary:**

The challenge with direct pod-to-pod communication in Kubernetes is that pods can frequently be spawned, deleted, and recreated, leading to the need to constantly rediscover IP addresses. To address this, Kubernetes introduces a new resource called a "service." 

**Key Points:**

- A service in Kubernetes acts as a stable endpoint that routes requests to one or multiple pods based on the configuration.
  
- Instead of needing to rediscover individual pod IP addresses, applications can interact with a service, which remains constant even when pods are recreated.
  
- Services can also provide load balancing. Requests can be evenly distributed among multiple pods to potentially improve response times and avoid resource contention.
  
- Kubernetes offers various types of services:
  - **Cluster IP:** Internal service available within the cluster, provides a stable IP and routes requests to a set of pods.
  - **Node Port:** Routes requests from outside the cluster to a service, though more complex routing solutions like Ingress are often preferred.
  - **Load Balancer:** Integrates with external load balancers or cloud providers to distribute traffic among pods.
  
- A key advantage of using services is the stability they offer. Even if individual pods are destroyed or relocated, the service remains consistent, making it a reliable intermediary for routing requests to the appropriate pods.

### Discovering Kubernetes Services

**Summary:**

Kubernetes services allow for efficient communication between pods and services. To use a Kubernetes service, a method of discovery is required. DNS (Domain Name System) is a preferred way to discover and communicate with Kubernetes services.

**Key Points:**

- **DNS in Kubernetes**:
  - DNS translates readable domain names (e.g., example.com) into IP addresses.
  - Kubernetes uses CoreDNS for domain name resolution.
  - Each new service in Kubernetes gets a DNS entry, making the service accessible by its name.
  
- **Service Naming**:
  - Service names are crucial for communication. 
  - Within the same namespace, services can be accessed using their name, like `nginx-service`.
  - For services in different namespaces, the format `service.namespace` is used, e.g., `nginx-service.front`.
  - The fully qualified domain name is structured as `service.namespace.svc.cluster.local`.
  
- **Environment Variables**:
  - Kubernetes injects environment variables for services.
  - To obtain the IP address of a service, the environment variable format is: `SERVICE_NAME_SERVICE_HOST`.
  - To get the port, the format is: `SERVICE_NAME_SERVICE_PORT`, e.g., `NGINX_PROVIDER_SERVICE_PORT`.

In most cases, service names or service names with namespaces are sufficient for communication. However, for applications requiring specific IP addresses or ports, Kubernetes provides environment variables.

