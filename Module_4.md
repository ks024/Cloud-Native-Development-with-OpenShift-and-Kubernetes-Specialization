# Implementing Colud Development Strategies
Compare different Cloud Deployment Strategies.

**Learning Objectives**
- Review what deployment strategies can be used in the Cloud, what they are used for and their advantages.

---
## Deployment Strategies in Kubernetes

### Lecture Notes: Strategies for Updating Applications in Kubernetes


1. **Introduction to Update Strategies:**
   - Updating applications in a Kubernetes cluster is crucial for managing workloads.
   - Kubernetes offers two main strategies: using deployment management and advanced routing strategies.

2. **Deployment Strategies:**
   - Two primary strategies for updating managed pods:
     - **Rolling Update**
     - **Recreate**

3. **Rolling Update Strategy:**
   - Updates pods incrementally from version 1 to version 2.
   - Process:
     - Edit deployment to update the image from version 1 to version 2.
     - Kubernetes creates a new pod with version 2.
     - Once the new pod is ready (passes readiness checks), an old pod is terminated.
     - This process repeats until all pods are updated to version 2.
   - Advantages:
     - Zero downtime during the update.
     - Version 1 pods are available until version 2 pods are ready.
   - Requirement:
     - Application must support running multiple versions simultaneously.

4. **Recreate Strategy:**
   - Immediately terminates all existing pods and then creates new pods with the updated version.
   - Process:
     - Deployment switches from version 1 to version 2.
     - All version 1 pods are terminated simultaneously.
     - New version 2 pods are created and initialized.
   - Disadvantages:
     - There is a period when no pods are available to serve requests.
   - Use Cases:
     - Application cannot run multiple versions simultaneously.
     - Persistent volumes or stateful logic prevent concurrent versions.
     - Immediate update is prioritized over downtime.

5. **Configuring Deployment Strategies:**
   - Deployment strategies are defined in the deployment spec.
   - Example configuration for rolling update:
     - Set strategy type to `RollingUpdate`.
     - Configure `maxSurge` and `maxUnavailable` parameters:
       - `maxSurge`: Number of additional pods that can be created during the update.
       - `maxUnavailable`: Number of old pods that can be terminated during the update.
   - Example: For 4 replicas, `maxSurge: 50%` allows 2 additional pods, `maxUnavailable: 25%` allows 1 pod to be terminated at a time.

6. **Viewing and Describing Deployment Strategies:**
   - Use `kubectl describe deployment <deployment-name>` to view strategy type and parameters.
   - Allows monitoring and tweaking deployment strategies as needed.

This lecture covers the essential strategies for updating applications in a Kubernetes cluster, highlighting the processes, advantages, and configurations of rolling updates and recreate strategies.

---
## Implementing Advanced Deployment Strategies Using the Kubernetes Router
### Lecture Notes: Advanced Deployment Strategies in Kubernetes

1. **Introduction to Routing Strategies:**
   - Focus on how users interact with the application in the cluster.
   - Two effective strategies: Blue-green deployment and A/B deployment.

2. **Blue-green Deployment Strategy:**
   - Involves two identical deployments: Blue and Green.
   - **Initial State:**
     - Blue deployment serves the application through the Blue service.
     - Ingress router directs traffic to the Blue service.
   - **Update Process:**
     - Create a Green deployment identical to Blue.
     - Develop and test new features in the Green deployment.
     - Once confident, switch the ingress router to direct traffic to the Green service.
     - Users now access the Green deployment with new features.
   - **Synchronization:**
     - Update Blue deployment with the changes from Green to keep them identical.
     - Future updates can start in Blue, followed by switching back to Blue after testing.
   - **Advantages:**
     - Simplified switch-over by changing router links.
     - Thorough testing in a production-like environment.

3. **A/B Deployment Strategy:**
   - Involves routing traffic to different versions of the application.
   - **Initial State:**
     - Ingress router directs traffic to a service managing multiple pods.
     - Pods are managed by two deployments with different versions (e.g., Version 1 and Version 2).
   - **Update Process:**
     - Some users receive responses from Version 1, others from Version 2.
     - Monitor logs, user engagement, and statistics from Version 2.
     - Gradually increase traffic to Version 2 as confidence grows.
     - Scale down Version 1 deployment accordingly.
   - **Advantages:**
     - Allows for gradual rollout and testing in a live environment.
     - Ability to monitor and analyze performance before full deployment.
   - **Advanced Implementations:**
     - Use service mesh for more complex routing based on cookies, HTTP headers, etc.
     - Advanced routing techniques without service mesh using basic Kubernetes objects.

4. **Summary:**
   - Blue-green deployment offers a straightforward switch between environments.
   - A/B deployment allows gradual rollout and user-based testing.
   - Both strategies can be implemented in various ways, depending on the needs and tools available in the Kubernetes environment.

These notes cover the essentials of using routing strategies to manage application updates in Kubernetes, highlighting the processes, advantages, and configurations of Blue-green and A/B deployment strategies.

---
## Guided Exercise: Implementing Cloud Deployment Strategies

### Guided Exercise: Deployment Strategies in Kubernetes

1. **Introduction:**
   - Explore deployment strategies for the deployment resource in Kubernetes.
   - Operating in the `username-dev` namespace with no existing resources.

2. **Creating the Deployment:**
   - Use `kubectl` to create a deployment with five replicas from a specified image.
   - Immediately describe the deployment to inspect default update strategy settings.

3. **Update Strategy Analysis:**
   - Default strategy type is rolling update with parameters:
     - **MaxSurge:** Allows up to 125% of configured replicas (5 pods).
     - **MaxUnavailable:** Can destroy up to 25% (1 pod) at a time during updates.

4. **Checking Initial State:**
   - Verify five pods are running using `kubectl get pods`.
   - Monitor changes with `kubectl get pods -w`.

5. **Updating the Deployment:**
   - Edit the deployment by incrementing the image tag from `version 1-external` to `version 2-external`.
   - Save and exit the YAML file to apply the update.

6. **Deployment Update Process:**
   - Monitor terminal output to observe Kubernetes managing pods:
     - Terminating existing pods gradually.
     - Spawning new pods to maintain the specified replica count.

7. **Verification and Cleanup:**
   - Confirm application update by checking logs for `Node.js` application version `2.0`.
   - Clean up by deleting the deployment with a simple `kubectl delete` command.

This exercise demonstrates how Kubernetes manages deployment updates using the default rolling update strategy, ensuring continuous availability of applications during the update process.

---
## Course Conclusion Highlights

In this concluding video, the instructor reflects on the extensive journey through Kubernetes concepts covered in the course:

1. **Introduction to Kubernetes Management:**
   - Explored why Kubernetes is pivotal in managing applications efficiently.

2. **Networking and Exposing Applications:**
   - Discussed methods for exposing applications within Kubernetes clusters and routing external requests.

3. **Probes and Application Readiness:**
   - Detailed the significance of liveness and readiness probes in ensuring application health and readiness to handle requests.

4. **Configuration Management:**
   - Emphasized the separation of application configuration from its core logic, enhancing flexibility and scalability.

5. **Deployment Strategies:**
   - Investigated various strategies for deploying new versions of applications in Kubernetes, including rolling updates and advanced routing techniques.

**Course Recap and Future Endeavors:**
- Acknowledged the breadth of knowledge acquired and commended learners for their progress.
- Encouraged further exploration of Kubernetes by suggesting enrollment in a subsequent course focusing on Red Hat OpenShift, offering deeper insights into Kubernetes operations.

This course serves as a foundational exploration of Kubernetes, preparing learners for more advanced topics and applications in cloud-native development and deployment.