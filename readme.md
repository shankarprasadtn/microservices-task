# Microservices Kubernetes Deployment using Minikube

## Overview

This project demonstrates deployment of a Node.js based microservices application on Kubernetes using Minikube.

The application contains the following services:

| Service | Port |
|---|---|
| User Service | 3000 |
| Product Service | 3001 |
| Order Service | 3002 |
| Gateway Service | 3003 |

The deployment includes:

- Kubernetes Deployments
- Kubernetes Services
- Resource Requests and Limits
- Liveness and Readiness Probes
- Internal Service Communication
- Optional Ingress Configuration

---

# System Requirements

## Operating System

- Ubuntu / Linux
- Windows with WSL2
- macOS

---

# Required Software Installation

## 1. Install Docker

### Ubuntu

```bash
sudo apt update

sudo apt install docker.io -y
```

Enable Docker:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Verify installation:

```bash
docker --version
```

---

## 2. Install kubectl

Download kubectl:

```bash
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
```

Make executable:

```bash
chmod +x kubectl
```

Move to system path:

```bash
sudo mv kubectl /usr/local/bin/
```

Verify installation:

```bash
kubectl version --client
```

---

## 3. Install Minikube

Download Minikube:

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

Install Minikube:

```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Verify installation:

```bash
minikube version
```

---

# Start Kubernetes Cluster

Start Minikube:

```bash
minikube start --driver=docker
```

Check cluster status:

```bash
minikube status
```

Expected output:

```text id="72p8t4"
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

---

# Enable Ingress Controller (Optional Bonus)

Enable ingress addon:

```bash
minikube addons enable ingress
```

Verify ingress controller:

```bash
kubectl get pods -n ingress-nginx
```

---

# Project Structure

```text id="3kt0xd"
submission/
├── deployments/
│   ├── user-service.yaml
│   ├── product-service.yaml
│   ├── order-service.yaml
│   └── gateway-service.yaml
├── services/
│   ├── user-service.yaml
│   ├── product-service.yaml
│   ├── order-service.yaml
│   └── gateway-service.yaml
├── ingress/
│   └── ingress.yaml
├── screenshots/
│   ├── pods.png
│   ├── logs.png
│   └── service-test.png
└── README.md
```

---

# Build Docker Images

Configure terminal to use Minikube Docker daemon:

```bash
eval $(minikube docker-env)
```

Build User Service image:

```bash
docker build -t user-service:latest ./user-service
```

Build Product Service image:

```bash
docker build -t product-service:latest ./product-service
```

Build Order Service image:

```bash
docker build -t order-service:latest ./order-service
```

Build Gateway Service image:

```bash
docker build -t gateway-service:latest ./gateway-service
```

Verify images:

```bash
docker images
```

---

# Deploy Application to Kubernetes

## Apply Deployments

```bash
kubectl apply -f deployments/
```

## Apply Services

```bash
kubectl apply -f services/
```

## Apply Ingress (Optional)

```bash
kubectl apply -f ingress/
```

---

# Verify Kubernetes Resources

## Check Pods

```bash
kubectl get pods
```

Expected:

```text id="kzjlwm"
All pods should be in Running state
```

---

## Check Services

```bash
kubectl get svc
```

---

## Check Deployments

```bash
kubectl get deployments
```

---

# Validate Inter-Service Communication

## Test User Service from Order Service

```bash
kubectl exec -it deployment/order-service -- curl http://user-service:3000
```

---

## Test Product Service from Order Service

```bash
kubectl exec -it deployment/order-service -- curl http://product-service:3001
```

---

## Test Order Service from Gateway Service

```bash
kubectl exec -it deployment/gateway-service -- curl http://order-service:3002
```

---

# Access Application Using Local IP

Get Minikube IP:

```bash
minikube ip
```

Example output:

```text id="a4gjmo"
192.168.49.2
```

---

# Expose Gateway Service

Temporarily expose Gateway Service using NodePort:

```bash
kubectl expose deployment gateway-service \
--type=NodePort \
--port=3003 \
--target-port=3003 \
--name=gateway-nodeport
```

Check service:

```bash
kubectl get svc
```

Example output:

```text id="uljlwm"
NAME               TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)
gateway-nodeport   NodePort   10.98.10.10    <none>        3003:32003/TCP
```

---

# Access Gateway Service from Browser

Use the following URL:

```text id="4l6vym"
http://192.168.49.2:32003
```

Example API routes:

```text id="c0hzps"
http://192.168.49.2:32003/api/users

http://192.168.49.2:32003/api/products

http://192.168.49.2:32003/api/orders
```

---

# Ingress Testing (Optional Bonus)

Check ingress:

```bash
kubectl get ingress
```

Get Minikube IP:

```bash
minikube ip
```

Example:

```text id="0n1m31"
192.168.49.2
```

Edit hosts file.

Linux/macOS:

```bash
sudo nano /etc/hosts
```

Windows:

```text id="fs8c1v"
C:\Windows\System32\drivers\etc\hosts
```

Add:

```text id="06xk6m"
192.168.49.2 micro.local
```

Test ingress routes:

```text id="95c2to"
http://micro.local/api/users

http://micro.local/api/products

http://micro.local/api/orders
```

---

# View Logs

Gateway Service logs:

```bash
kubectl logs deployment/gateway-service
```

Order Service logs:

```bash
kubectl logs deployment/order-service
```

User Service logs:

```bash
kubectl logs deployment/user-service
```

---

# Troubleshooting

## Pod CrashLoopBackOff

```bash
kubectl logs <pod-name>
```

```bash
kubectl describe pod <pod-name>
```

---

## ImagePullBackOff Error

```bash
eval $(minikube docker-env)
```

Rebuild images if required.

---

## Service Not Reachable

```bash
kubectl get svc
```

```bash
kubectl get endpoints
```

---

## Restart Deployment

```bash
kubectl rollout restart deployment gateway-service
```

---

# Cleanup

Delete deployments:

```bash
kubectl delete -f deployments/
```

Delete services:

```bash
kubectl delete -f services/
```

Delete ingress:

```bash
kubectl delete -f ingress/
```

Stop Minikube:

```bash
minikube stop
```

Delete Minikube cluster:

```bash
minikube delete
```

---

# Screenshots

| Screenshot | Command |
|---|---|
| Running Pods | kubectl get pods |
| Running Services | kubectl get svc |
| Logs | kubectl logs deployment/gateway-service |
| Local IP Test | Browser output using Minikube IP |
| Ingress Test | Browser output |

Store screenshots inside:

```text id="0z9j0v"
screenshots/
```

---

# Expected Outcome

- All pods running successfully
- Services communicating internally
- Gateway accessible using local Minikube IP
- APIs reachable from browser
- Ingress routing working correctly
- Health probes passing

---

# Submission

Create final ZIP file:

```text id="8s8qq4"
submission.zip
```

Include:

- Deployment YAML files
- Service YAML files
- Ingress YAML file
- README.md
- Screenshots

---
