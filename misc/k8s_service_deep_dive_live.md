[Video Link](https://www.youtube.com/watch?v=fCX8O7GA_lY&ab_channel=Abhishek.Veeramalla)

### Summary
In this video, Abhishek introduces the topic of Kubernetes services and explains the practical aspects of load balancing, service discovery, and application exposure to the outside world within a Kubernetes cluster.

**Highlights**
* 0:00 👋 Introduction and course update
* 0:30 📚 Deep dive into Kubernetes services
* 1:15 🧪 Practical session on load balancing, service discovery, and application exposure
* 2:10 📈 Using Cube shark for traffic viewing
* 3:00 🔍 Exploring traffic flow and component communication within Kubernetes

**Key Insights**
- 🌐 Kubernetes services provide load balancing and service discovery capabilities within a cluster.
- 🚀 Practical demonstrations enhance understanding of traffic flow and component communication.
- 🌍 Applications can be exposed both within the cluster and to the outside world using Kubernetes services.

## For demo purpose. We are using minicube cluster.

To view minikube cluster status use `minikube status` in cmd. This will show our minikube cluster status which needs to be up & running. 

For getting the info on all the kubernetes resources `kubectl get all`. Make sure `service/kubernetes` is running.

For the demo pupose we are using this [repo:](https://github.com/iam-veeramalla/Docker-Zero-to-Hero.git)

Let's get inside the `Docker-Zero-to-Hero/examples/python-web-app` directory. Here we will find the Dockerfile as:
```python
FROM ubuntu

WORKDIR /app

COPY requirements.txt /app
COPY devops /app

RUN apt-get update && \
    apt-get install -y python3 python3-pip && \
    pip install -r requirements.txt && \
    cd devops

ENTRYPOINT ["python3"]
CMD ["manage.py", "runserver", "0.0.0.0:8000"]

```
Now let's build a image with command `docker build -t rajtmg/python-sample-app-demo:v1 .` This will create a image. 

### Deployment
Now for creating a deployment we have the following code for the `deployment.yml` file.
```python
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-python-app
  labels:
    app: sample-python-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: sample-python-app
  template:
    metadata:
      labels:
        app: sample-python-app
    spec:
      containers:
      - name: python-app
        image: abhishekf5/python-sample-app-demo:v1
        ports:
        - containerPort: 8000
```
* **Deployments:**
    - Create a Deployment: `kubectl create -f deployment.yml`
    - Get Deployments: `kubectl get deployments`
    - Describe Deployment: `kubectl describe deployment <deployment_name>`
    - Scale Deployment: `kubectl scale --replicas=3 deployment/<deployment_name>`

- `kubectl get pods -o wide`: Show the pods along with their IP.

*Now we can access the application within our cluster.*
Let's get inside the minikube cluster with cmd: `minikube ssh`. We can chect the application status with `curl -L http://<pod_id>/8000/demo`








### Some command k8s commands:
- `kubectl get deploy`: Shows the deployment list
- `kubectl get pods`: Shows the list of available pods.
- `kubectl get pods -o wide`: Show the pods along with their IP.
- `kubectl get all`: Show the resources running.
