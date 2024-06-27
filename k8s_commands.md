## Basic Kubectl commands which are essential for a DevOps engineer to manage within a Kubernetes cluster.

* **Pods:**
    - Create a Pod: `kubectl create -f pod.yaml`
    - Get Pods: `kubectl get pods`
    - Describe Pod: `kubectl describe pod <pod_name>`
    - Logs: `kubectl logs <pod_name>`
    - Exec into Pod: `kubectl exec -it <pod_name> -- <command>`
    - Delete Pod: `kubectl delete pod <pod_name>`

* **Deployments:**
    - Create a Deployment: `kubectl create -f deployment.yaml`
    - Get Deployments: `kubectl get deployments`
    - Describe Deployment: `kubectl describe deployment <deployment_name>`
    - Scale Deployment: `kubectl scale --replicas=3 deployment/<deployment_name>`
    - Rollout Status: `kubectl rollout status deployment/<deployment_name>`
    - Rollout History: `kubectl rollout history deployment/<deployment_name>`

* **Services:**
    - Create a Service: `kubectl create -f service.yaml`
    - Get Services: `kubectl get services`
    - Describe Service: `kubectl describe service <service_name>`
    - Delete Service: `kubectl delete service <service_name>`

* **ConfigMaps:**
    - Create a ConfigMap: `kubectl create configmap <configmap_name> --from-file=<file_path>`
    - Get ConfigMaps: `kubectl get configmaps`
    - Describe ConfigMap: `kubectl describe configmap <configmap_name>`
    - Delete ConfigMap: `kubectl delete configmap <configmap_name>`

* **Secrets:**
    - Create a Secret: `kubectl create secret generic <secret_name> --from-literal=<key>=<value>`
    - Get Secrets: `kubectl get secrets`
    - Describe Secret: `kubectl describe secret <secret_name>`
    - Delete Secret: `kubectl delete secret <secret_name>`

* **Nodes:**
    - Get Nodes: `kubectl get nodes`
    - Describe Node: `kubectl describe node <node_name>`

* **Namespaces:**
    - Get Namespaces: `kubectl get namespaces`
    - Describe Namespace: `kubectl describe namespace <namespace_name>`
    

* **PersistentVolumes (PV) and PersistentVolumeClaims (PVC):**
    - Get PVs/PVCs: `kubectl get pv / kubectl get pvc`
    - Describe PV/PVC: `kubectl describe pv <pv_name> / kubectl describe pvc <pvc_name>`
    - Delete PV/PVC: `kubectl delete pv <pv_name> / kubectl delete pvc <pvc_name>`

[More Tips:](https://drive.google.com/file/d/19oHgltLJOsexyQBEbjjEbIobaIZ8ygFW/view)

Make sure your kubectl context refers to a namespace where you have enough
permissions, usually username-dev or username-stage. Use the 
```sh
kubectl config set-context --current --namespace=namespace 
```
command to switch to the appropriate namespace.

**Validate it.**
```sh
kubectl config view --minify | grep namespace:
```