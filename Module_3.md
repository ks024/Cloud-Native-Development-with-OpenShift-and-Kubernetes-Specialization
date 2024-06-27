## Customize Deployments for Application Requirements
Advanced container management features.

**Learning Objectives**

- Leverage how to avoid applications overusing system resources.
- Review how Kubernetes evaluates application health status via probes and automatic application restart.
- Create Kubernetes resources holding application configuration and secrets, and how to make that configuration available to running applications.


### Defining Resource Requests and Limits for Pods + Viewing Requests, Limits, and Actual Usage

- **Introduction to Resource Limiting:**
  - Resource limiting in Kubernetes is crucial for managing pod resource consumption.
  - Pods are containerized processes managed by Kubernetes, which isolates and wraps containers within pods.

- **Resource Requests and Limits:**
  - **Resource Requests:**
    - Minimum resources required by a pod.
    - Ensures a pod is scheduled only on a node with sufficient resources.
  - **Resource Limits:**
    - Maximum resources a pod can consume.
    - Prevents a pod from using more resources than allocated.

- **Configuring Resources in a YAML Manifest:**
  - Within a pod's specification (`spec`), resources are configured under the container object.
  - Example configuration:
    ```yaml
    resources:
      requests:
        cpu: "10m"  # 10 millicores
        memory: "20Mi"  # 20 megabytes
      limits:
        cpu: "80m"  # 80 millicores
        memory: "100Mi"  # 100 megabytes
    ```
  - **Millicores:**
    - CPU resources are measured in millicores (m).
    - 1 CPU = 1000 millicores.

- **Setting Resources Using kubectl:**
  - Resources can be set directly in the YAML file or using kubectl commands.
  - Example command:
    ```sh
    kubectl set resources deployment <deployment_name> --requests=cpu=10m,memory=20Mi --limits=cpu=80m,memory=100Mi
    ```

- **Viewing Resource Requests and Limits:**
  - **Per Pod:**
    - Detailed view of resources used by an individual pod.
  - **Per Node:**
    - Describing a node shows the resource requests and limits of all pods on that node.
    - Example command:
      ```sh
      kubectl describe node <node_name>
      ```
    - Shows total resource requests and limits for all pods on the node.

- **Using Metrics API:**
  - **kubectl top command:**
    - Requires the metrics API to be installed.
    - Provides real-time resource usage metrics.
    - Example command:
      ```sh
      kubectl top node <node_name>
      kubectl top pod <pod_name>
      ```

- **Summary:**
  - Limiting resource usage in Kubernetes ensures efficient resource management.
  - Proper configuration of resource requests and limits helps maintain cluster stability and performance.


### Resource Quotas in Kubernetes

- **Objective:**
  - Manage and limit resource usage for multiple teams sharing a Kubernetes cluster.

- **Problem:**
  - Limiting resources for individual pods is insufficient when multiple teams use the same cluster.
  - Teams may consume resources disproportionately, leading to contention.

- **Solution:**
  - Use Kubernetes resource quotas to enforce limits on resource usage.

- **Types of Quotas:**
  - **Object Counts:**
    - Limit the number of Kubernetes resources that can be created (e.g., pods, services).
  - **Compute Resources:**
    - Limit the total amount of compute resources (e.g., CPU, memory, storage) that can be used within a namespace.

- **Purpose:**
  - Prevent resource contention among multiple teams on a single Kubernetes cluster.

- **Resource Quota Manifest:**
  - Define resource quotas using a new Kubernetes resource type called `ResourceQuota`.
  - Example manifest file structure:
    ```yaml
    apiVersion: v1
    kind: ResourceQuota
    metadata:
      name: example-quota
    spec:
      hard:
        pods: "10"
        requests.cpu: "1.3"
        requests.memory: "1.5Gi"
        services: "10"
    ```

- **Creating a Resource Quota:**
  - Apply the manifest file using `kubectl create` or `kubectl apply`.
  - Command example:
    ```sh
    kubectl create -f example-quota.yaml
    ```

- **Alternative Method:**
  - Use `kubectl` to generate a quota resource:
    ```sh
    kubectl create quota example-quota --hard=pods=10,services=10,requests.cpu=1.3,requests.memory=1.5Gi
    ```

- **Managing Quotas:**
  - View existing quotas using `kubectl get resourcequotas`.
  - Command example:
    ```sh
    kubectl get resourcequotas
    ```

- **Important Considerations:**
  - Quotas may not affect existing resources in a namespace.
  - Modifying a resource that exceeds the quota will be denied.
  - Quotas are essential for developers to understand when actions are denied due to resource limits.

- **Practical Tip:**
  - When encountering denied actions, check if a namespace-bound quota restricts the resource usage.

### Summary:
- Resource quotas help manage resource allocation for multiple teams within a single Kubernetes cluster.
- Quotas can limit both the number of Kubernetes resources and the total compute resources consumed.
- Understanding and managing resource quotas is crucial for preventing resource contention and ensuring fair resource distribution among teams.


### Kubernetes LimitRange

- **Purpose:**
  - Define acceptable ranges for resource limits and requests for containers within a Kubernetes cluster.

- **Resource Definition:**
  - **Kind:** LimitRange
  - **Spec Section:**
    - Specifies limits for different Kubernetes resources (e.g., pods, containers, persistent volume claims).

- **Container Type Configuration:**
  - **Maximum Limits:**
    - CPU: 500 millicores
    - Memory: 750 megabytes
  - **Minimum Limits:**
    - CPU: 10 millicores
    - Memory: 5 megabytes
  - **Default Limits:**
    - CPU: 100 millicores
    - Memory: 100 megabytes
  - **Default Requests:**
    - CPU: 20 millicores
    - Memory: 20 megabytes

- **Benefits:**
  - Prevents individual containers from consuming excessive resources.
  - Ensures fair resource distribution among multiple teams using the same cluster.
  - Automatically sets default resource limits and requests if not specified by developers.

- **Practical Use Cases:**
  - Restricting resource consumption to avoid one team monopolizing resources.
  - Enforcing reasonable resource usage standards across the cluster.
  - Setting automatic resource configurations for developers who do not specify them.

- **Implementation:**
  - Create the LimitRange resource using a YAML manifest:
    ```yaml
    apiVersion: v1
    kind: LimitRange
    metadata:
      name: example-limitrange
    spec:
      limits:
      - type: Container
        max:
          cpu: "500m"
          memory: "750Mi"
        min:
          cpu: "10m"
          memory: "5Mi"
        default:
          cpu: "100m"
          memory: "100Mi"
        defaultRequest:
          cpu: "20m"
          memory: "20Mi"
    ```
  - Apply the manifest using `kubectl create -f example-limitrange.yaml`.
  - Retrieve and describe the LimitRange using `kubectl get limitrange` and `kubectl describe limitrange`.

- **Key Points for Developers:**
  - Be aware of LimitRange configurations as they can impact application behavior in production environments.
  - Understand the importance of setting appropriate resource limits and requests to ensure optimal performance and resource utilization.

- **Commands:**
  - Create LimitRange: `kubectl create -f example-limitrange.yaml`
  - Get LimitRange: `kubectl get limitrange`
  - Describe LimitRange: `kubectl describe limitrange`
  - Delete LimitRange: `kubectl delete -f example-limitrange.yaml` (if privileges allow)

### Conclusion:
- LimitRange is an essential resource for managing and enforcing resource usage policies within a Kubernetes cluster.
- It helps maintain balanced resource distribution and prevents resource contention among different teams.
- Developers should understand and leverage LimitRange to optimize application deployment and resource management in Kubernetes.


### Guided Exercise: Limiting Resource Usage in Kubernetes

- **Objective:**
  - Demonstrate how Kubernetes handles deployments requesting excessive resources.

- **Steps:**

  1. **Create Deployment:**
     - Use kubectl to create a deployment named `hello-limits` with a specified image.
     - Command:
       ```sh
       kubectl create deployment hello-limits --image=<image> -o yaml > hello-limits.yaml
       ```

  2. **Edit YAML File:**
     - Add a resources section to the container specification.
     - Set resource requests to 8 CPUs and 20 MB of memory.
     - Example YAML:
       ```yaml
       resources:
         requests:
           cpu: "8"
           memory: "20Mi"
       ```

  3. **Apply Deployment:**
     - Apply the edited YAML file to create the deployment.
     - Command:
       ```sh
       kubectl apply -f hello-limits.yaml
       ```

  4. **Check Pod Status:**
     - Observe that the pod is in a pending state.
     - Troubleshoot using `kubectl describe pod` to identify the issue.
     - Issue: Insufficient CPU resources (8 CPUs not available on any node).

  5. **Troubleshoot Using Events:**
     - Use `kubectl get events --field-selector type=Warning` to check for scheduling failures.
     - Confirm that the pod failed to schedule due to insufficient CPU resources.

  6. **Modify Resource Requests:**
     - Adjust CPU request to 1200 millicores (1.2 CPUs) and reapply the deployment.
     - Commands:
       ```sh
       kubectl apply -f hello-limits.yaml
       ```

  7. **Check and Modify Again:**
     - If the pod is still pending, further reduce CPU request to 120 millicores (0.12 CPUs).
     - Apply the updated configuration.
     - Commands:
       ```sh
       kubectl apply -f hello-limits.yaml
       ```

  8. **Verify Pod Status:**
     - Confirm that the pod is running with the updated resource requests.

  9. **Cleanup:**
     - Delete the deployment using the YAML file.
     - Command:
       ```sh
       kubectl delete -f hello-limits.yaml
       ```

- **Summary:**
  - Resource requests and limits help manage pod resource consumption.
  - Troubleshooting involves checking pod status and cluster events.
  - Adjusting resource requests to match available resources ensures successful pod scheduling.
  - Cleanup is performed by deleting the deployment using the YAML file.


### Proving Liveness, Readiness and Startup

### Kubernetes Probes

**Purpose:**
- Determine the readiness and health of applications deployed in a Kubernetes cluster.

**Current Application State Monitoring:**
- Previously, the status check was limited to whether the node hosting the application was alive or if the container exited in an error state.
- More granular checks are needed to determine temporary connection losses, application errors, and configuration errors.

**Types of Probes in Kubernetes:**

1. **Startup Probe:**
   - **Usage:** Executed at the beginning of the pod's lifecycle.
   - **Function:** Determines if the pod is ready to serve requests.
   - **Example:** For a database application, the startup probe ensures the database is ready to receive connections before marking the pod as ready.
   - **Behavior:**
     - Runs once and stops after passing.
     - If it fails repeatedly within a certain threshold, the pod is considered faulty and can be restarted based on the restart policy.

2. **Readiness Probe:**
   - **Usage:** Runs continuously throughout the pod's lifecycle.
   - **Function:** Checks if the pod is ready to serve requests at any given time.
   - **Behavior:**
     - If it fails, the pod is marked as not ready, and no requests are routed to it.
     - If it passes, the pod is marked as ready, and requests are routed to it.
     - Continuously evaluated and can mark the pod as not ready if it fails at any future point.

3. **Liveness Probe:**
   - **Usage:** Runs continuously throughout the pod's lifecycle.
   - **Function:** Detects if the application is in an unhealthy state (e.g., due to memory leaks or application freezes).
   - **Behavior:**
     - If it fails, the pod is considered unhealthy and is restarted.
     - Continuously evaluated to ensure the application remains healthy.

**Configuring Probes:**
- **Failure Threshold:** Sets the number of allowed failures before the pod is considered faulty.
- **Success Threshold:** Sets the number of required successes to mark the probe as passed.
- **Parameters:** Various parameters are available to fine-tune the behavior of the probes according to application requirements.

**Summary:**
- **Startup Probe:** Runs once at the beginning, determines initial readiness.
- **Readiness Probe:** Runs continuously, determines if the pod can serve requests.
- **Liveness Probe:** Runs continuously, detects and addresses unhealthy states by restarting the pod.

**Implementation:**
- Use Kubernetes manifest files to configure and apply these probes.
- Monitor the status and behavior of these probes to ensure application health and readiness in the Kubernetes cluster.

### Methods of Checking Application Health + Creating Probes

**Overview:**
- Probes are used to determine the health and readiness of applications in Kubernetes.
- Common methods to implement probes include HTTP checks, container execution checks, and TCP socket checks.

**Methods of Implementing Probes:**

1. **HTTP Checks:**
   - **Usage:** Commonly used for applications exposing HTTP interfaces like REST APIs.
   - **Configuration:**
     - Define the probe type (e.g., readiness probe).
     - Specify HTTP method (e.g., GET request).
     - Set the path within the application and the port.
     - Configure parameters such as initial delay and timeout.
   - **Example:** Readiness probe with a GET request to `/healthz` on port 8080 with a 15-second initial delay and a 1-second timeout.

2. **Container Execution Checks:**
   - **Usage:** Execute commands within the pod to determine readiness or liveness.
   - **Configuration:**
     - Define the probe type (e.g., liveness probe).
     - Use the `exec` command to run specific commands within the container.
     - Commands are specified as an array of strings.
   - **Example:** Execute a SQL command like `SELECT 1` to check if a database is ready to accept connections.

3. **TCP Socket Checks:**
   - **Usage:** For applications that communicate via TCP ports instead of HTTP.
   - **Configuration:**
     - Define the probe type.
     - Specify the port to check.
     - If a successful connection is established, the check passes; otherwise, it fails.
   - **Example:** Check if a TCP connection can be established on a specified port.

**Implementation Steps in Kubernetes:**
- Use the `kubectl edit` command to modify resources like deployments.
- Add the probe type (readiness, liveness, or startup) to the container configuration.
- Specify parameters and implementation details for the probe.
- Example Configuration:
  ```yaml
  readinessProbe:
    httpGet:
      path: /healthz
      port: 8080
      scheme: HTTP
    initialDelaySeconds: 15
    timeoutSeconds: 1
  ```

**Key Points:**
- HTTP checks are widely used and easy to implement if the application exposes an HTTP API.
- Container execution checks are useful for specific command execution within the pod.
- TCP socket checks are ideal for applications using TCP communication.
- Probes can be added or modified easily using Kubernetes commands and manifest files.

**Next Steps:**
- Practice implementing these probes in a guided exercise within the Kubernetes cluster to see how they work in practice.

### Guided Exercise: Proving Liveness, Readiness and Startup

1. **Introduction to Probes:**
   - Deploy a simple application with two endpoints: `/health` for Liveness probe and `/ready` for Readiness probe.
   - `/ready` endpoint takes around 30 seconds to return a successful status code (200), initially returns 503 (failure).

2. **Creating Deployment:**
   - Use `kubectl create` command to create a deployment named `do100-probes` with the provided image.

3. **Exposing Deployment:**
   - Expose the deployment using `kubectl expose`, setting the service name as `do100-probes` and port to 8080.

4. **Creating Ingress Resource:**
   - Navigate to the `probes` directory in the `do100x-apps` repository.
   - Verify and edit `probes-ingress.yml` to set the correct host, path (`/`), pathType (`Prefix`), service name (`do100-probes`), and port (8080).
   - Create the ingress using `kubectl create`.

5. **Configuring Probes:**
   - Edit the deployment YAML to add Liveness and Readiness probes.
   - Liveness probe uses `/health` endpoint on port 8080.
   - Readiness probe uses `/ready` endpoint on port 8080 with an initial delay of 15 seconds and a timeout of 2 seconds.
   - Use `kubectl apply` to apply the updated deployment configuration.

6. **Understanding Probe Behavior:**
   - Observe new pods being created and old pods terminating after the readiness probe passes.
   - Ensure proper YAML indentation when configuring probes.

7. **Simulating Probe Failures:**
   - Use `kubectl logs -f` to monitor logs and observe probe checks.
   - Simulate Liveness probe failure by making `/health` endpoint return an unhealthy state, causing pod restarts.
   - Simulate Readiness probe failure by making `/ready` endpoint return a failure state, making pods not ready without restarts.

8. **Effects of Probe Failures:**
   - Liveness probe failure causes pod restarts after three unhealthy responses.
   - Readiness probe failure marks the pod as not ready, preventing Kubernetes from routing traffic to it.

9. **Cleaning Up:**
   - Delete all resources: deployment, service, pods, replica sets, and ingress using `kubectl delete`.

10. **Conclusion:**
    - This exercise demonstrates the use of Liveness and Readiness probes in Kubernetes to manage application health and readiness states effectively.