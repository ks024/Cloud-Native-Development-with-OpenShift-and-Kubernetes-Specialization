# Week1

## Introduction

### Course Introduction:

**Summary:**

- **Instructor:** Marek Czernek, Senior Curriculum Developer at Red Hat.
- **Course Overview:** 
  - Focus on managing cloud-native applications with Kubernetes.
  - Emphasis on practical use of Kubernetes for application management.
  
- **Course Content:**
  1. Understanding managed applications and benefits of using deployment within Kubernetes.
  2. Networking within Kubernetes: 
     - Exposing applications for internal access.
     - Enabling external access via Ingress.
  3. Resource usage limitation and ensuring application readiness.
  4. Decoupling configuration from source code.
  5. Cloud deployment strategies and updating applications on Kubernetes.
  
- **Prerequisites:**
  - Completion of the first course in the specialization for Kubernetes cluster setup (strongly encouraged).
  - Alternative: Refer to appendices A and B for required configuration details if skipping the first course.
  
- **Environment Requirements:**
  - Supported Operating Systems: Linux, Windows, macOS.
  - Installations Required:
    - `kubectl` (essential)
    - `Minikube` (optional, but used by the instructor)
    - `Git` (useful for cloning the `deal 100x apps` repository)
  
- **Terminal and Command Styles:**
  - Guided exercises provided with various terminal prompts (Linux, macOS, Windows PowerShell).
  - Commands are designed to be universally applicable across supported systems.
  - For long commands:
    - Linux/macOS: Use the backslash (`\`) to break commands.
    - Windows: Use the backtick (`` ` ``) to break commands or type the command on a single line.

This introduction sets the stage for understanding Kubernetes and its practical applications in managing cloud-native applications, with specific focus on networking, resource management, and deployment strategies.


### Course Materials:

- **Course Material:**
  - Utilize the [DO100B-K1.22](https://d3c33hcgiwev3.cloudfront.net/X55DbdyTTvKeQ23ck87y5g_7d590cc1a3be4820a1a5146a839084f1_DO100B-K1.22-en-2-7067502.pdf?Expires=1712188800&Signature=e6QPBvpjmeSDqxsmBS2uGwMAji01VzKvKiK1bl5GdHthb1qbH-80HFQ2aPInC3frzH35hVH5XlV0stkYgRmKEtDPZzSzknGHsvsllGuBbl-jmOPSEfmFthyOPaw4bAc7~f8~CDlayi9grlL38bzUVEw-XHF5KQ9heYtLOGoJKXE_&Key-Pair-Id=APKAJLTNE6QMUY6HBC5A) PDF file.
  - Download and review relevant sections from the PDF corresponding to your module before attempting quizzes.

- **Quizzes:**
  - Designed to be challenging.
  - Require understanding of both text and video materials for full points.

- **Course Position:**
  - Second course in the cloud-native series.
  - If not completed, participants are encouraged to finish ["Foundations of Red Hat Cloud-native Development"](https://www.coursera.org/learn/foundations-of-red-hat-cloud-native-development) before starting this course.

- **Additional Resources:**
  - Red Hat sponsors and maintains the content on [kubernetesbyexample.com](https://kubebyexample.com/).
  - Course materials and supplementary articles can be found on the [website](https://kubebyexample.com/learning-paths/application-development-kubernetes/lesson-1-running-containerized-applications-1) for further clarification on concepts discussed in the course. We can find additional information in a series of articles published [here](https://kubebyexample.com/learning-paths/kubernetes-fundamentals/what-kubernetes-3-minutes).



## Deploying Managed Applications

### Managing Containers:
**Summary:**

In the introductory video of the course, the topic focuses on deploying managed applications with Kubernetes.

**Key Points:**

1. **Containerized Applications:** Applications are containerized to isolate them from the environment. However, Kubernetes operates at the pod level, which can contain one or multiple containers.
  
2. **Pod Behavior:** If an application within a pod crashes, the entire pod crashes. This behavior is not ideal as it requires manual intervention for restarting.

3. **Deployment Concept:** Kubernetes introduces the concept of a "deployment" which acts as a watcher or manager for pods. When a pod dies, the deployment spawns a new pod, ensuring continuous operation.

4. **Managed Applications:** Kubernetes manages applications declaratively. Developers specify what the application should look like, and Kubernetes ensures it matches that specification.

5. **Declarative vs. Imperative Approach:** 
    - **Declarative:** Developers specify the desired state, and Kubernetes ensures it matches.
    - **Imperative:** Developers specify exact steps and commands to execute.
  
6. **Manifest File:** Used in Kubernetes to specify:
    - Container images.
    - Number of replicas (pods).
    - Update strategies.

7. **Kubernetes Managers' Features:**
    - **Automatic Restart:** If a pod crashes, the deployment creates a new one.
    - **Automatic Rollout:** Updates container images without application downtime.
    - **Automatic Scaling:** Adjusts pod count based on resource consumption.
    - **Automatic Deployment:** Deploys specified pods based on developer requirements.

In essence, Kubernetes allows for the managed deployment of containerized applications through its various management resources, like deployments, providing benefits such as automatic restarts, updates, scaling, and deployment.


### Creating a Deployment:

**Summary:**

In Kubernetes, a deployment acts as a manager or manager loop that watches over pods. Here's how to create and manage deployments using `kubectl`:

1. **Creating a Deployment:**
    - Use `kubectl create` command.
    - Specify the resource as "deployment".
    - Provide a deployment name.
    - At minimum, provide an image for the container.
    - Optional: Set parameters like the number of replicas.

2. **Fetching Deployment Details:**
    - Use `kubectl get deployment <deployment_name>` to fetch deployment details.
    - `kubectl` responds with the deployment's status.
    - Use `--output yaml` to get the full YAML representation of the deployment, providing both its status and manifest.

3. **Understanding the YAML Representation:**
    - **Kind:** Indicates the type of resource, e.g., "Deployment".
    - **Metadata:** Contains the resource's name.
    - **Labels:** Key-value pairs that help categorize resources.
    - **Spec (Specification):** Defines the desired state of the deployment, like the number of replicas.
    - **Template:** Describes the pod specification.
        - **Containers:** An array indicating the container details, including the image and name.

4. **Deployment Status:**
    - Displays the current state of the deployment.
    - For instance, if the deployment specifies three replicas, the status will show the same.

The deployment manages pods, which in turn contain containers. While the names of pods and containers can be set, they are less significant when using single-container pods. The status of the deployment reflects its current state as per the deployment manifest.


### Understanding the Schema of a Deployment Resource

**Summary:**

This video focuses on understanding the deployment resource within Kubernetes:

1. **Replicas in Deployment:**
   - Specified under the `spec` section.
   - Denotes how many pods the deployment manager should spawn.
   - If a pod is destroyed, the deployment automatically replaces it to maintain the total number of pods.

2. **Deployment Strategy:**
   - Essential for updating applications.
   - Strategies aim to balance resource usage, responsiveness, and efficiency.
   - Kubernetes offers two default strategies:
     - **Rolling Update:** Gradually terminates old pods and spawns new ones.
       - Default values: 25% of pods can be terminated and spawned at once.
     - **Recreate:** Terminates all replicas and recreates them quickly. Useful for testing and staging.
   - Deployment strategy is specified under `spec.strategy.type`.

3. **YAML Resource Structure:**
   - `spec.replicas`: Denotes the number of pods.
   - `spec.strategy.type`: Sets the deployment strategy.
   - `template`: Defines the containers managed by the deployment.
     - Everything above the `template` section relates to the deployment.
     - Everything below the `template` section pertains to the pods the deployment spawns.
   - YAML indentation is crucial.

4. **Labels in Deployment:**
   - **Three Places for Labels:**
     1. Above the `template` section: Applies to the deployment itself.
     2. Below the `template` section: Applies to the pods.
     3. `selector` section: Used to match labels of pods that the deployment will manage.
   - Labels help deployments identify and manage specific pods.
   - Labels are key-value pairs, and the `selector` ensures the deployment manages the correct pods based on matching labels.

The video provides a comprehensive overview of how deployments function as managers within Kubernetes, ensuring the correct number of pods, managing updates efficiently, and using labels to identify and manage the appropriate pods.


### Guided Exercise Deploying Managed Applications:

This guided exercise provides a comprehensive overview of managing pods within a Kubernetes cluster using `kubectl`. Here's a summarized breakdown of the exercise:

**Objective:** Understand the deployment and management of pods within a Kubernetes cluster.

**Prerequisites:** 
- A working Kubernetes cluster.
- Familiarity with `kubectl` and how to communicate with the cluster.

**Steps:**

1. **Deployment Creation:**
    - Use `kubectl` to generate a deployment.
    - Command: `kubectl create deployment <deployment-name> --image=<image-name>`
    - Check the deployment status using: `kubectl get deployment`

2. **Pod Information:**
    - Check the status of pods: `kubectl get pods`
    - Retrieve detailed information about the deployment using:
        - `kubectl get deployment -o yaml`
        - `kubectl describe deployment <deployment-name>`

3. **Scaling the Deployment:**
    - Scale the deployment to have multiple replicas.
    - Command: `kubectl scale deployment <deployment-name> --replicas=<number-of-replicas>`
    - Verify the scaled pods: `kubectl get pods`

4. **Handling Pod Failures:**
    - Simulate a pod failure by deleting a pod.
    - Command: `kubectl delete pod <pod-name>`
    - Verify the pod recovery: `kubectl get pods`

5. **Updating the Deployment:**
    - Update the image of the deployment.
    - Command: `kubectl edit deployment <deployment-name>`
    - Update the image version and save.
    - Observe the rolling update behavior.

6. **Deployment Cleanup:**
    - Delete the deployment to clean up resources.
    - Command: `kubectl delete deployment <deployment-name>`
    - Verify the deletion: `kubectl get pods`

**Key Concepts Learned:**
- **Deployments**: They manage pods and ensure the desired number of pod replicas are running.
- **Pod Management**: Deployments automatically handle pod failures by creating new pods.
- **Scaling**: `kubectl` allows scaling the number of replicas for a deployment.
- **Updating**: Deployments support rolling updates to manage changes to the application.
- **Cleanup**: Deleting a deployment removes the associated pods.

**Conclusion:**
The exercise emphasizes the power and convenience of using deployments in Kubernetes to manage and maintain pod replicas, handle failures, scale applications, and perform updates efficiently.