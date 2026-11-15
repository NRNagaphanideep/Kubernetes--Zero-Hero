# 🚀 Kubernetes (K8s) Zero-to-Hero Study Notes

This document summarizes all the core concepts, YAML configurations, and `kubectl` CLI commands covered so far in our hands-on Kubernetes DevOps journey.

---

## 1. Kubernetes Architecture & Cluster Setup
* **Master Node (Control Plane):** The brain of the cluster (manages scheduling, state, and API requests).
* **Worker Nodes:** The machines where actual application containers (Pods) run.
* **Minikube:** A local single-node Kubernetes cluster for development and learning.
* **`kubectl`:** The primary CLI tool used to communicate with the Kubernetes API server.

### **Cluster Management Commands**
```bash
# Start local Minikube cluster using Docker driver
minikube start --driver=docker

# Check cluster status
minikube status

# View all nodes in the cluster
kubectl get nodes

# Check kubectl client version
kubectl version --client
```

---

## 2. Pods (The Building Blocks)
A **Pod** is the smallest deployable unit in Kubernetes. It encapsulates one or more containers sharing storage and network resources.

### **Imperative Pod Commands (CLI)**
```bash
# Create and run a single Pod directly
kubectl run my-first-pod --image=nginx:alpine --port=80

# List all running Pods
kubectl get pods

# View detailed information and events for a Pod
kubectl describe pod my-first-pod

# Delete a specific Pod
kubectl delete pod my-first-pod
```

### **Declarative Pod Configuration (`pod.yaml`)**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-yaml-pod
  labels:
    env: development
spec:
  containers:
  - name: my-nginx-container
    image: nginx:alpine
    ports:
    - containerPort: 80
```
```bash
# Create or update resources from a YAML file
kubectl apply -f pod.yaml

# Delete resources defined in a YAML file
kubectl delete -f pod.yaml
```

---

## 3. Deployments & ReplicaSets (Self-Healing & Scaling)
A **Deployment** manages stateless applications by ensuring a specified number of Pod replicas are always running. If a Pod crashes or is deleted, the Deployment automatically recreates it (**Self-Healing**).

### **Deployment Configuration (`deployment.yaml`)**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-web-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-web-app
  template:
    metadata:
      labels:
        app: my-web-app
    spec:
      containers:
      - name: nginx-container
        image: nginx:alpine
        ports:
        - containerPort: 80
```

### **Deployment Operations (Scaling & Rolling Updates)**
```bash
# Apply deployment configuration
kubectl apply -f deployment.yaml

# List active deployments
kubectl get deployments

# Scale the number of Pod replicas up or down
kubectl scale deployment my-web-deployment --replicas=5

# Perform a Zero-Downtime Rolling Update with a new image version
kubectl set image deployment/my-web-deployment nginx-container=nginx:1.25-alpine

# Check the status of a rolling update
kubectl rollout status deployment/my-web-deployment
```

---

## 4. Services & Networking
Because Pod IP addresses are dynamic and change whenever a Pod is recreated, a **Service** provides a stable network endpoint to expose Pods internally or externally.

* **`ClusterIP`:** Exposes the service on an internal IP within the cluster (default).
* **`NodePort`:** Exposes the service on each Node's IP at a static external port.
* **`LoadBalancer`:** Provisions an external cloud load balancer (AWS/GCP/Azure).

### **Service Commands**
```bash
# Expose a Deployment externally via NodePort
kubectl expose deployment my-web-deployment --type=NodePort --port=80

# List all active Services and their allocated ports
kubectl get services

# Automatically open a NodePort service in the browser (Minikube helper)
minikube service my-web-deployment
```

---

## 5. Troubleshooting & Monitoring Commands
Essential commands for debugging Pods, inspecting logs, and executing commands inside running containers.

```bash
# View all resources (Pods, Deployments, Services, ReplicaSets) in the default namespace
kubectl get all

# View standard output logs of a specific Pod
kubectl logs <pod-name>

# Stream live logs in real-time
kubectl logs -f <pod-name>

# Inspect detailed configuration, status, and error events (e.g., ImagePullBackOff)
kubectl describe pod <pod-name>

# Open an interactive shell inside a running container
kubectl exec -it <pod-name> -- /bin/sh
```
