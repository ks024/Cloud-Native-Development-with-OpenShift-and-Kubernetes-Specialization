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


### Guided Exercise: Exposing Applications for External Access

**Summary: Guided Exercise on Kubernetes Services**

- **Objective**: Understand how to use services for networking between applications in Kubernetes.

- **Applications Deployed**:
  - **name-generator**: Exposes a `random-name` endpoint returning a random name.
  - **email-generator**: Utilizes the `random-name` endpoint from `name-generator` to generate a random email.

- **Communication**:
  - `email-generator` communicates with `name-generator` via a GET request to `NAME_GENERATOR_URL`.
  
- **Deployment**:
  - Both applications are present in the `D0100x-apps` [repository](https://github.com/RedHatTraining/DO100-apps).
  - Deployed using Kubernetes manifests (`deployment.yml`).
  - `name-generator` has three replicas and serves on port 8080.
  - `email-generator` has three replicas and serves on port 8081.
  - Each app has it's own `deployment.yml` file within it's directory.
  - For creating a deploymnet the command would be: `kubectl apply -f deployment.yml`

- **Services Creation**:
  - **name-generator service**: Exposed on port 80, targets port 8080.
  - Command for simply creating service from the cmd.
  `kubectl expose deployment name-generator --port 80 --target-port 8080`
  - **email-generator service**: Exposed on port 80, targets port 8081.
  - Created from the `service.yml` file located in the directory. `kubectl create -f service.yml`
  - Both services are of type `ClusterIP` and are internal to the Kubernetes cluster.
  
- **Testing the Setup**:
  - Created a new pod named `curl` in a different namespace.
  - Used `kubectl run` to spawn this pod with an interactive shell.
  - Attempted to query the `email-generator` service.
  - Corrected DNS resolution by including the namespace (`username-dev`).
  - Successfully accessed the `random-email` endpoint of `email-generator` from the `curl` pod.

```bash
kubectl run -n mczernek-stage curl --it --rm --image=registry.access.redhat.com/ubi8/ubi-minimal -- sh
```
OR
```bash
kubectl run -n mczernek-stage curl --stdin --tty --rm --image=registry.access.redhat.com/ubi8/ubi-minimal -- sh
```
**Description**:
* `-n mczernek-stage`: defines a new namespace.
* curl: defines a pod name.
* --it : Runs pod in iterative mode We can use `--stdin --tty` also:
* --rm : Removes the pod when exited.
* --image : Image used for the pod.
* -- sh : Opens in shell

- **Cleanup**:
  - Deleted deployments and services using `kubectl delete`.
    - `Kubectl delete deployments --all` : Deletes all deployment at once.
  - Cluster is now empty with no pods.

The exercise provides a comprehensive walkthrough of deploying applications, creating services, and testing communication between them in a Kubernetes environment.

### Exposing Applications for External Access: Ingress Resources

#### Kubernetes Ingress + Kubernetes Ingress Controller

- **Introduction to Ingress:**
  - Ingress is used to route external traffic into a Kubernetes cluster.
  - It helps in managing external access to services within the cluster.
  
- **Internal Communication:**
  - Pods communicate using their own IP addresses.
  - Services are used for internal routing within the cluster.

- **Need for Ingress:**
  - Provides a solution for routing external traffic.
  - Allows users outside the data center to access applications within the cluster.

### Enable Nginx Ingress Controller in Minikube

1. **Enable the Ingress Addon**:
   - Run the following command to enable the Nginx Ingress controller in Minikube:
     ```sh
     minikube addons enable ingress
     ```

2. **Verify the Ingress Controller**:
   - Wait a few moments for the Ingress controller to be deployed. You can check the status with:
     ```sh
     kubectl get pods -n kube-system | grep ingress
     ```
   - You should see pods related to the Ingress controller running, such as `ingress-nginx-controller`.

3. **Verify Ingress Controller Service**:
   - Ensure that the Ingress controller service is running:
     ```sh
     kubectl get svc -n kube-system | grep ingress
     ```
   - This should show a service like `ingress-nginx-controller` with an external IP or a node port.

### After Enabling Ingress Controller

Once the Ingress controller is up and running, try the following steps:

1. **Verify Ingress Resource**:
   - Ensure your Ingress resource is created and recognized by the Ingress controller:
     ```sh
     kubectl get ingress
     kubectl describe ingress hello
     ```

  - **Ingress Extension:** Includes the Ingress resource and Ingress controller.
  - **Ingress Controller:** Acts as a load balancer for external requests.

- **Functionality of Ingress Controller:**
  - Handles external traffic at the edge of the cluster.
  - Routes requests based on domain names to specific pods.
  - For example, requests to `example.com` could be routed to pod 1, and requests to `test.com` could be routed to pod 2.

- **Routing Process:**
  - External request reaches the Ingress controller.
  - Ingress controller routes the request to the appropriate pod.
  - The pod processes the request and sends a response back through the Ingress controller to the originator.

- **Usage:**
  - Typically managed by system administrators.
  - Developers use the Ingress resource within the cluster.
  - Important for developers to understand its presence and functionality.

- **Summary:**
  - Ingress is crucial for managing external access to Kubernetes applications.
  - It simplifies routing and load balancing of external traffic to internal pods.

#### Ingress Resource Configuration + Testing Your Ingress

- **Introduction to Ingress Resource**
  - The Ingress resource is a standard Kubernetes resource used to route traffic into a Kubernetes cluster.
  - It has its own manifest file in YAML format.

- **Manifest File Structure**
  - **Kind**: Specifies the resource type as `Ingress`.
  - **Name**: Defines the name of the Ingress resource.
  - **Rules Section**: Critical for traffic routing.
    - Takes an array of rules indicated by a dash (-).
    - Key elements include `host`, `path`, and `pathType`.

- **Path Types**
  - **Prefix**: Matches the specified path and anything that follows it.
  - **Exact**: Matches the exact path and host.

- **Example Configuration**
  - **Host**: `hello.example.com`
  - **Path**: `/` (with path type `Prefix`)
    - Matches requests like `hello.example.com/login`.
  - **Backend Configuration**: 
    - Specifies the service to route the request to.
    - Example: Service name `hello`, service port `80`.

- **Testing the Configuration**
  - Can be tested using a web browser, curl, or specifying the host header if DNS resolution is set up.
  - Use the DNS hostname or include the host header if DNS entries are not available.

- **Conclusion**
  - The Ingress resource effectively routes external traffic into the Kubernetes cluster according to the defined rules and backend configuration.

### Guided Exercise: Exposing Applications for External Access: Ingress Resources

**Overview:**
- The focus is on routing external requests to applications within a Kubernetes cluster using the Ingress Resource.
- A simple hello application is deployed to demonstrate this process.

**Steps to Implement Ingress Resource:**

1. **Create Deployment:**
   - Use `kubectl create` to create a deployment named `hello` with a specified container image and three replicas.
   - Verify the deployment and ensure all pods are in the running state.

2. **Expose Deployment as a Service:**
   - Use `kubectl expose` to create a service for the `hello` deployment.
   - Service listens on port 8080 and redirects traffic to port 8080 within the pod.
   - Verify the service configuration and endpoints.

3. **Create Ingress Resource:**
   - Navigate to the directory containing the ingress manifest file (`ingress-hello.yml`).
   - Update the host field in the manifest file to match the configured host (e.g., `hello.example.com`).
   - Apply the ingress manifest using `kubectl apply -f ingress-hello.yml`.
   - Verify the ingress resource and its host configuration.

4. **Testing Ingress:**
   - Use `curl` to send requests to the specified host (e.g., `hello.example.com`) to verify routing.
   - Check responses to ensure they are coming from the correct pod IP addresses.
   - Verify pod IP addresses using `kubectl get pods -o wide`.

5. **Ingress Configuration for OpenShift:**
   - Determine the correct host path for OpenShift by combining resource name, namespace, and the wildcard DNS entry of the OpenShift router.
   - Update the ingress manifest file with the correct host path.
   - Apply the ingress resource and verify it using `curl`.

6. **Cleanup:**
   - Delete the created resources (Ingress, deployment, and service) using `kubectl delete`.
   - Verify that no resources remain in the namespace.

**Key Points:**
- **Deployment:** Creation and verification of deployment and pods.
- **Service Exposure:** Creating a service to expose the deployment.
- **Ingress Resource:** Configuration and application of ingress to route external traffic.
- **Verification:** Testing ingress routing using `curl` and verifying pod IP addresses.
- **OpenShift Configuration:** Specific steps to configure ingress in an OpenShift environment.
- **Cleanup:** Proper deletion of resources to maintain a clean namespace.

**Final Note:**
- Understanding the process of routing external requests into a Kubernetes cluster using Ingress.
- Practical application and verification of Ingress functionality.
