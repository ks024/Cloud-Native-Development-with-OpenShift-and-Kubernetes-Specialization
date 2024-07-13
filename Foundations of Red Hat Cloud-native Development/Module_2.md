## Contrasting Kubernetes Distributions

### Learning Objectives

* See the differences between several Kubernetes implementations, and understand how to prepare different Kubernetes flavours for this course.
* Review the basic usage of the kubectl command and understand how to connect to your Kubernetes cluster by using the CLI.
* Execute a pre-built application in your Kubernetes cluster and review the resources related to the process.

## Understanding Different Kubernetes Distributions

### Overview of Kubernetes Distributions

In this video, we explore different Kubernetes distributions, each tailored for specific environments and use cases:

1. **Cloud-Based Kubernetes:**
   - **Google Kubernetes Engine (GKE):** Kubernetes deployed on Google Cloud.
   - **Amazon EKS:** Kubernetes deployed on Amazon Cloud.
   - **OpenShift Dedicated:** A cloud-based version of Red Hat OpenShift.

2. **On-Premises Kubernetes:**
   - **Red Hat OpenShift:** Can be installed on local data centers.
   - **VMware Tanzu and Rancher:** Other examples of on-premises Kubernetes solutions.

3. **Local Machine Kubernetes:**
   - **Minikube:** Deploy Kubernetes on local machines, suitable for learning and development.
   - **CodeReady Containers:** Another local deployment option for learning purposes.

4. **Small-Scale Cloud Kubernetes:**
   - **Developer Sandbox:** A free, small-scale version of OpenShift in the cloud, suitable for learning.

### Why Different Kubernetes Versions?

Different distributions exist to cater to various needs and environments:
- **On-Premises vs. Cloud:** Some distributions are designed for on-premises deployment (e.g., Red Hat OpenShift), while others are cloud-based (e.g., Amazon EKS).
- **Scale and Purpose:** Distributions range from large-scale production-ready solutions to small-scale learning environments.
- **Feature Sets:** Each distribution may offer unique features tailored to its intended use case.

### Key Differences Among Distributions

1. **DNS Extension:**
   - CoreDNS is commonly used across many Kubernetes distributions for name resolution.
   - Essential for translating domain names to IP addresses.

2. **Dashboard (GUI):**
   - **Minikube:** Uses the dashboard extension.
   - **OpenShift:** Uses the console extension with a different look and functionality.

3. **Ingress Controllers:**
   - **Minikube:** Uses the NGINX controller via an add-on.
   - **OpenShift:** Uses an HAProxy-based controller managed by the ingress operator.

4. **Storage Solutions:**
   - **Minikube:** Uses the storage provisioner add-on.
   - **OpenShift:** Offers Red Hat OpenShift Data Foundation for storage.

5. **Authentication and Authorization:**
   - **Minikube:** Provides an administrator account by default.
   - **OpenShift:** Has security restrictions by default to prevent running potentially dangerous workloads as administrators.

6. **Operators:**
   - Custom controllers for additional functionality.
   - Not enabled by default in Minikube but heavily relied upon in OpenShift.

### Conclusion

Different Kubernetes distributions cater to various environments, from cloud to on-premises and local machines. They provide similar core functionalities but differ in their implementations and additional features. Understanding these differences helps in selecting the right Kubernetes distribution for specific needs and use cases.

---
## Introducting Kubectl

### Learning Objectives
- Install and configure a Kubernetes cluster locally using Minikube or the Red Hat OpenShift Developer Sandbox.

### Introduction

In this guided exercise, you'll have two options to deploy a Kubernetes cluster:
1. **Minikube**: Suitable if your machine has sufficient resources.
2. **Red Hat OpenShift Developer Sandbox**: Ideal if you face difficulties with Minikube or have limited resources.

### Installing Minikube

1. **Overview**:
   - Minikube is a single binary that spawns a Kubernetes cluster.
   - It supports Linux, macOS, and Windows.

2. **Installation Steps** (Linux Example):
   - Install required packages:
     ```sh
     sudo dnf install @virtualization libvirt qemu-kvm
     sudo systemctl enable --now libvirtd
     sudo dnf install minikube
     ```
   - Verify Minikube installation:
     ```sh
     minikube -h
     ```

3. **Starting Minikube**:
   - Start the cluster:
     ```sh
     minikube start
     ```
   - Verify cluster status:
     ```sh
     minikube status
     ```
   - Enable Ingress extension:
     ```sh
     minikube addons enable ingress
     ```

4. **Configuring Hostname Resolution**:
   - Get Minikube IP address:
     ```sh
     minikube ip
     ```
   - Edit the hosts file to map the IP to `hello.example.com`:
     ```sh
     sudo nano /etc/hosts
     # Add the following line:
     <minikube-ip> hello.example.com
     ```

### Using Red Hat OpenShift Developer Sandbox

1. **Accessing the Sandbox**:
   - Visit [Red Hat Developer Sandbox](https://developers.redhat.com/developer-sandbox) and log in with your Red Hat account (registration is free).

2. **Navigating the Sandbox**:
   - Once logged in, you'll see the Red Hat OpenShift user interface.
   - Note: You will not have administrator access, and you can only use the pre-created projects.

### Summary

By following this guided exercise, you should have:
- Installed and configured Minikube on your local machine, or
- Accessed the Red Hat OpenShift Developer Sandbox if Minikube installation was not feasible.

### Next Steps

Now that your Kubernetes cluster is up and running, you are ready to start exploring `kubectl` and how to control your Kubernetes cluster. Stay tuned for the next video where we'll dive deeper into these topics.


## Guided Exercise: Controlling Your Kubernetes Cluster with `kubectl`

### Introduction

In this video, we will learn how to control a Kubernetes cluster using `kubectl`, a command-line utility for interacting with Kubernetes. `kubectl` allows you to send requests to your Kubernetes cluster and receive responses in a formatted manner.

### Learning Objectives
- Install `kubectl` on your system.
- Configure `kubectl` to communicate with your Kubernetes cluster.
- Execute basic `kubectl` commands to interact with your cluster.

### Installing `kubectl`

1. **Installation Steps**:

   - **Linux**:
     ```sh
     curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
     chmod +x kubectl
     sudo mv kubectl /usr/local/bin/
     ```

   - **macOS**:
     ```sh
     brew install kubectl
     ```

   - **Windows**:
     - Download the latest release from [Kubernetes official release page](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-windows).
     - Add the binary to your PATH.

### Configuring `kubectl`

`kubectl` uses a `KUBECONFIG` file located in your home directory (`~/.kube/config` by default) to store configuration information.

1. **Configuration Concepts**:
   - **Clusters**: Define the Kubernetes cluster's address and port.
   - **Users**: Define who is communicating with the cluster, including authentication methods (tokens, basic auth, certificates).
   - **Contexts**: Bind a cluster and a user together, potentially including a default namespace.

2. **Example Configuration**:
   ```yaml
   apiVersion: v1
   clusters:
   - cluster:
       server: https://<cluster-ip>:<port>
     name: my-cluster
   contexts:
   - context:
       cluster: my-cluster
       user: my-user
     name: my-context
   current-context: my-context
   users:
   - name: my-user
     user:
       client-certificate: /path/to/client.crt
       client-key: /path/to/client.key
   ```

3. **Configuring via `kubectl config`**:
   ```sh
   kubectl config set-cluster my-cluster --server=https://<cluster-ip>:<port>
   kubectl config set-credentials my-user --client-certificate=/path/to/client.crt --client-key=/path/to/client.key
   kubectl config set-context my-context --cluster=my-cluster --user=my-user
   kubectl config use-context my-context
   ```

### Basic `kubectl` Commands

1. **Version Command**:
   ```sh
   kubectl version
   ```
   - Displays the version of the `kubectl` client and the Kubernetes server.

2. **Get Command**:
   ```sh
   kubectl get pods
   ```
   - Retrieves a list of all pods in the current namespace.

   ```sh
   kubectl get pods <pod-name>
   ```
   - Retrieves details of a specific pod.

3. **Delete Command**:
   ```sh
   kubectl delete pod <pod-name>
   ```
   - Deletes a specified pod.

4. **Apply Command**:
   ```sh
   kubectl apply -f <resource-file.yaml>
   ```
   - Creates or updates a resource based on the specified file.

### Summary

In this exercise, you have:
- Installed `kubectl` on your system.
- Configured `kubectl` to communicate with your Kubernetes cluster.
- Executed basic `kubectl` commands to interact with your cluster.

### Next Steps

Now that you have a basic understanding of `kubectl`, we will continue exploring more advanced commands and concepts in subsequent videos and exercises. This will provide you with hands-on practice and a deeper understanding of how to manage your Kubernetes clusters effectively.


## Guided Exercise: Installing and Configuring `kubectl`

### Introduction

Welcome to our next guided exercise. Now that we are familiar with `kubectl`, let's install it on our system and connect it to our Kubernetes cluster. Depending on your operating system, the installation process will differ. Hopefully, you have your Kubernetes cluster installed and deployed.

### Installation Steps

#### Linux

1. **Download and Install `kubectl`**:
   ```sh
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   chmod +x kubectl
   sudo mv kubectl /usr/local/bin/
   ```

2. **Verify Installation**:
   ```sh
   kubectl version --client
   ```
   - You should see output indicating the client version, confirming successful installation.

#### macOS

1. **Download and Install `kubectl`**:
   ```sh
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"
   chmod +x kubectl
   sudo mv kubectl /usr/local/bin/
   ```

2. **Alternative Installation Using Homebrew**:
   ```sh
   brew install kubectl
   ```

3. **Verify Installation**:
   ```sh
   kubectl version --client
   ```
   - You should see output indicating the client version, confirming successful installation.

#### Windows

1. **Download `kubectl`**:
   - Download the latest release from the [Kubernetes release page](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-windows).

2. **Add `kubectl` to PATH**:
   - Add the directory where you placed `kubectl` to your PATH environment variable.

3. **Verify Installation**:
   ```sh
   kubectl version --client
   ```
   - You should see output indicating the client version, confirming successful installation.

### Configuring `kubectl`

To configure `kubectl`, we provide scripts in the `DO100x-apps` repository. The repository is located on GitHub. We recommend installing Git to download this repository.

#### Clone the Repository

1. **Install Git** (if not already installed):
   - Visit the [Git download page](https://git-scm.com/downloads) and follow the instructions for your operating system.

2. **Clone the Repository**:
   ```sh
   git clone https://github.com/username/DO100x-apps.git
   cd DO100x-apps
   ```

#### Configure `kubectl` for Minikube

1. **Navigate to Setup Directory**:
   ```sh
   cd setup/linux
   ```

2. **Run the Setup Script**:
   ```sh
   ./setup.sh
   ```
   - This script will configure `kubectl` with the necessary namespaces, certificates, and credentials.

3. **Verify Configuration**:
   ```sh
   kubectl get pods
   ```
   - You should see output indicating the pods running in your cluster, confirming successful configuration.

#### Configure `kubectl` for OpenShift Developer Sandbox

1. **Obtain Login Command**:
   - Visit the Developer Sandbox environment.
   - Click your username and copy the login command.

2. **Run the Setup Script**:
   ```sh
   ./setup_sandbox.sh
   ```
   - Provide the OpenShift cluster URL, Token, and username when prompted.

3. **Verify Configuration**:
   ```sh
   kubectl get pods
   ```
   - You should see output indicating the pods running in your cluster, confirming successful configuration.

#### Windows

1. **Navigate to Windows Directory**:
   ```powershell
   cd setup/windows
   ```

2. **Run the Setup Script**:
   ```powershell
   ./setup_sandbox.ps1
   ```
   - Provide the necessary inputs (OpenShift cluster URL, Token, and username).

3. **Verify Configuration**:
   ```powershell
   kubectl get pods
   ```
   - You should see output indicating the pods running in your cluster, confirming successful configuration.

### Conclusion

At this point, you should have `kubectl` installed and configured correctly on your system, ready to interact with your Kubernetes cluster. In the next video, we will explore how to deploy containerized applications to our Kubernetes cluster. See you in the next video!

---
## Running and Interacting with Your First Application
## Deploying Workloads to a Kubernetes Cluster

### Introduction

In this video, we will discuss how to deploy workloads to our Kubernetes cluster. Previously, we set up our Kubernetes cluster and configured `kubectl`. Now, we will learn how to use our cluster by deploying our first workload.

### Kubernetes and Containerized Applications

Kubernetes manages containerized applications using a concept called **pods**. A pod is the smallest deployable unit in Kubernetes and can contain one or multiple containers. Here's a brief overview:

1. **Containerized Application**: A process running in isolation (e.g., Python, Java).
2. **Pod**: Wraps one or more containers. Kubernetes manages pods, not individual containers.

### Deploying an Application

#### Using `kubectl run`

The easiest way to create a workload in your Kubernetes cluster is by using the `kubectl run` command. This command creates a pod with a specified name and image.

1. **Command**:
   ```sh
   kubectl run <name> --image=<image>
   ```

2. **Example**:
   ```sh
   kubectl run my-app --image=nginx
   ```
   - This creates a pod named `my-app` with a single container using the `nginx` image.

#### Using YAML/JSON Manifest Files

A more flexible and scalable way to deploy applications is by using manifest files, which describe the Kubernetes resources.

1. **Generating a Resource Manifest**:
   - Use `kubectl create` with the `--dry-run=client` and `-o yaml` options to generate a YAML manifest for a resource.

2. **Command**:
   ```sh
   kubectl create deployment <name> --image=<image> --dry-run=client -o yaml
   ```

3. **Example**:
   ```sh
   kubectl create deployment web-server --image=nginx --dry-run=client -o yaml > deployment.yaml
   ```
   - This generates a YAML file named `deployment.yaml` describing a deployment named `web-server`.

4. **Applying the Manifest**:
   - Use `kubectl apply` to create or update the resource described in the manifest file.

5. **Command**:
   ```sh
   kubectl apply -f <file>
   ```

6. **Example**:
   ```sh
   kubectl apply -f deployment.yaml
   ```

### Difference Between `kubectl create` and `kubectl apply`

1. **`kubectl create`**:
   - Attempts to create the resource. If the resource already exists, it fails.
   - Example:
     ```sh
     kubectl create -f deployment.yaml
     ```

2. **`kubectl apply`**:
   - Tries to create the resource. If the resource exists, it updates the resource to match the new definition.
   - Example:
     ```sh
     kubectl apply -f deployment.yaml
     ```

### Interacting with Pods

After creating pods, you might want to execute commands inside them for debugging or troubleshooting purposes.

#### Using `kubectl exec`

1. **Executing a Command**:
   - Use `kubectl exec` to run a command inside a pod.
   
2. **Command**:
   ```sh
   kubectl exec <pod-name> -- <command>
   ```

3. **Example**:
   ```sh
   kubectl exec my-pod -- ls /app
   ```

4. **Opening an Interactive Shell**:
   - Use `kubectl exec -it` to open an interactive shell inside a pod.

5. **Command**:
   ```sh
   kubectl exec -it <pod-name> -- /bin/sh
   ```

6. **Example**:
   ```sh
   kubectl exec -it my-pod -- /bin/sh
   ```

### Conclusion

In this video, we learned about deploying workloads to our Kubernetes cluster using `kubectl run` and manifest files. We also explored the difference between `kubectl create` and `kubectl apply`, and how to interact with pods using `kubectl exec`. In the next video, we will dive deeper into deploying containerized applications in Kubernetes. See you in the next video!

## Guided Exercise: Running and Interacting with Your First Application

1. **Setup and Verification**:
   - Ensure Kubernetes cluster and `kubectl` are configured.
   - Verify namespace with `kubectl get pods`.

2. **Deploying a Pod**:
   - Use `kubectl run` to create a pod:
     ```sh
     kubectl run webserver --image=registry.access.redhat.com/httpd
     ```

3. **Checking Pod Status**:
   - Check the status with:
     ```sh
     kubectl get pods
     ```

4. **Executing Commands in the Pod**:
   - Print `httpd.conf` file content:
     ```sh
     kubectl exec webserver -- cat /etc/httpd/conf/httpd.conf
     ```
   - Open an interactive shell:
     ```sh
     kubectl exec -it webserver -- /bin/bash
     ```

5. **Creating a Pod Manifest File**:
   - Generate YAML manifest without creating the pod:
     ```sh
     kubectl run probes --image=quay.io/redhattraining/do100-probes:external --dry-run=client -o yaml > probes.yml
     ```

6. **Creating the Pod from the Manifest**:
   - Create the pod using the manifest file:
     ```sh
     kubectl create -f probes.yml
     ```
   - Monitor pod status:
     ```sh
     kubectl get pods -w
     ```

7. **Modifying the Manifest File**:
   - Add a new label (ensure proper indentation):
     ```yaml
     labels:
       app: do100-probes
     ```
   - Apply the changes:
     ```sh
     kubectl apply -f probes.yml
     ```

8. **Verifying Labels**:
   - Show labels with:
     ```sh
     kubectl get pods --show-labels
     ```
   - Describe pod for detailed information:
     ```sh
     kubectl describe pod probes
     ```

9. **Cleaning Up**:
   - Delete the created pods:
     ```sh
     kubectl delete pod webserver
     kubectl delete pod probes
     ```
   - Verify no pods are running:
     ```sh
     kubectl get pods
     ```

These key points summarize the steps for deploying and managing containerized applications in a Kubernetes cluster.