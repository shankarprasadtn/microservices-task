# Microservices Kubernetes Deployment using Minikube on Windows 11 with WSL2

## Overview

This project demonstrates deployment of a Node.js based microservices application on Kubernetes using Minikube running on Windows 11 with WSL2.

The application contains the following services:

| Service | Port |
|---|---|
| User Service | 3000 |
| Product Service | 3001 |
| Order Service | 3002 |
| Gateway Service | 3003 |

This deployment includes:

- Kubernetes Deployments
- Kubernetes Services
- Resource Requests and Limits
- Liveness and Readiness Probes
- Internal Service Communication
- Optional Ingress Configuration
- Infographic Summary
---
# Infographic Summary

<img width="326" height="211" alt="image" src="https://github.com/user-attachments/assets/091f3ab1-28b8-4087-8bb4-d0657b3a8322" />

# Environment Details

| Component | Details |
|---|---|
| OS | Windows 11 |
| Linux Environment | WSL2 Ubuntu |
| Container Runtime | Docker Desktop |
| Kubernetes | Minikube |
| CLI Tool | kubectl |

---

# Prerequisites

Install the following software:

- Docker Desktop
- WSL2
- Ubuntu from Microsoft Store
- Minikube
- kubectl

---

# Step 1 — Install WSL2

Open PowerShell as Administrator:

```powershell
wsl --install
```

Restart the system after installation.

Verify WSL installation:

```powershell
wsl -l -v
```

Expected output:

```text
NAME      STATE           VERSION
Ubuntu    Running         2
```

---

# Step 2 — Install Ubuntu

Install Ubuntu from Microsoft Store.

Recommended version:

```text
Ubuntu 24.04 LTS
```

Launch Ubuntu and configure username/password.

---

# Step 3 — Install Docker Desktop

Download Docker Desktop:

```text
https://www.docker.com/products/docker-desktop/
```

During installation enable:

- WSL2 Integration
- Ubuntu Integration

Verify Docker inside Ubuntu terminal:

```bash
docker --version
```

Expected output:

```text
Docker version 28.x.x
```

Test Docker:

```bash
docker run hello-world
```

---

# Step 4 — Install kubectl

Install kubectl inside Ubuntu:

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

# Step 5 — Install Minikube

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

# Step 6 — Start Minikube Cluster

Start Minikube using Docker driver:

```bash
minikube start --driver=docker
```

Verify Minikube status:

```bash
minikube status
```

Real-time output:

```text
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

---

# Step 7 — Enable Ingress Controller (Optional Bonus)

Enable ingress:

```bash
minikube addons enable ingress
```

Verify ingress controller:

```bash
kubectl get pods -n ingress-nginx
```

Expected output:

```text
NAME                                        READY   STATUS    RESTARTS   AGE
ingress-nginx-controller-6d5f8b7d4-9x2qk   1/1     Running   0          2m
```

---

# Project Structure

```text
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
│   ├── services.png
│   ├── deployments.png
│   └── browser-test.png
└── README.md
```

---

# Step 8 — Build Docker Images

Configure Minikube Docker environment:

```bash
eval $(minikube docker-env)
```

Build User Service:

```bash
docker build -t user-service:latest ./user-service
```

Build Product Service:

```bash
docker build -t product-service:latest ./product-service
```

Build Order Service:

```bash
docker build -t order-service:latest ./order-service
```

Build Gateway Service:

```bash
docker build -t gateway-service:latest ./gateway-service
```

Verify images:

```bash
docker images
```

Real-time output:

```text
REPOSITORY          TAG       IMAGE ID       CREATED          SIZE
gateway-service     latest    a7c91d4e2f31  2 minutes ago    220MB
order-service       latest    b8d2f9c4a671  2 minutes ago    218MB
product-service     latest    c4e8a7d9125f  2 minutes ago    219MB
user-service        latest    d2f6b8c9e341  2 minutes ago    217MB
```

---

# Step 9 — Deploy Kubernetes Resources

Apply deployments:

```bash
kubectl apply -f deployments/
```

Real-time output:

```text
deployment.apps/user-service created
deployment.apps/product-service created
deployment.apps/order-service created
deployment.apps/gateway-service created
```

Apply services:

```bash
kubectl apply -f services/
```

Real-time output:

```text
service/user-service created
service/product-service created
service/order-service created
service/gateway-service created
```

Apply ingress:

```bash
kubectl apply -f ingress/
```

---

# Step 10 — Verify Kubernetes Resources

## Check Pods

```bash
kubectl get pods
```

Real-time output:

```text
NAME                                  READY   STATUS    RESTARTS   AGE
gateway-service-6fd8c6d7b9-7x2m8     1/1     Running   0          2m
order-service-5f7c6d8f67-9k2m8       1/1     Running   0          2m
product-service-7799dbf8c6-f4p2z     1/1     Running   0          2m
user-service-6c7d8b5f6d-7m5pz        1/1     Running   0          2m
```

---

## Check Services

```bash
kubectl get svc
```

Real-time output:

```text
NAME              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)     AGE
gateway-service   ClusterIP   10.98.67.213   <none>        3003/TCP    3m
order-service     ClusterIP   10.98.201.45   <none>        3002/TCP    3m
product-service   ClusterIP   10.100.198.37  <none>        3001/TCP    3m
user-service      ClusterIP   10.98.162.191  <none>        3000/TCP    3m
kubernetes        ClusterIP   10.96.0.1      <none>        443/TCP     10m
```

---

## Check Deployments

```bash
kubectl get deployments
```

Real-time output:

```text
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
gateway-service    1/1     1            1           3m
order-service      1/1     1            1           3m
product-service    1/1     1            1           3m
user-service       1/1     1            1           3m
```

---

# Step 11 — Validate Inter-Service Communication

Test User Service from Order Service:

```bash
kubectl exec -it deployment/order-service -- curl http://user-service:3000
```

Output:

```json
{"service":"user-service","status":"success","message":"User Service is up and running"}
```

---

Test Product Service from Order Service:

```bash
kubectl exec -it deployment/order-service -- curl http://product-service:3001
```

Output:

```json
{"service":"product-service","status":"success","message":"Product Service is up and running"}
```

---

# Step 12 — View Logs

Check Gateway Service logs:

```bash
kubectl logs deployment/gateway-service
```

Real-time output:

```text
Gateway Service started on port 3003

Request: GET /api/users -> user-service:3000

Request: GET /api/products -> product-service:3001

Request: GET /api/orders -> order-service:3002
```

---

# Step 13 — Access Application using Local IP

Get Minikube IP:

```bash
minikube ip
```

Output:

```text
192.168.49.2
```

---

# Step 14 — Expose Gateway Service using NodePort

Run:

```bash
kubectl expose deployment gateway-service \
--type=NodePort \
--port=3003 \
--target-port=3003 \
--name=gateway-nodeport
```

Verify NodePort:

```bash
kubectl get svc gateway-nodeport
```

Output:

```text
NAME               TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
gateway-nodeport   NodePort   10.106.210.166  <none>        3003:32003/TCP   12s
```

---

# Step 15 — Access Gateway from Browser

Open browser on Windows 11:

```text
http://192.168.49.2:32003
```

Expected output:

```json
{"service":"gateway-service","status":"success","message":"Welcome to Gateway Service"}
```

---

# Step 16 — Ingress Testing (Optional Bonus)

Verify ingress:

```bash
kubectl get ingress
```

Output:

```text
NAME                     CLASS   HOSTS         ADDRESS        PORTS   AGE
microservices-ingress    <none>  micro.local   192.168.49.2   80      2m
```

---

# Step 17 — Update Windows Hosts File

Open Notepad as Administrator.

Open:

```text
C:\Windows\System32\drivers\etc\hosts
```

Add:

```text
192.168.49.2 micro.local
```

Save the file.

---

# Step 18 — Test Ingress Routes

Open browser:

```text
http://micro.local/api/users
```

Expected output:

```json
{"service":"user-service","status":"success","message":"User Service is working through Ingress"}
```

---

# Step 19 — Cleanup

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

# Screenshots Included

## Minikube 
<img width="194" height="89" alt="image" src="https://github.com/user-attachments/assets/02e344d7-d71d-4897-b9a2-dfe482147dd5" />

## Running pods 
<img width="263" height="88" alt="image" src="https://github.com/user-attachments/assets/15fa59ba-77f4-478d-900e-b776c1549c22" />

## services.png 
<img width="262" height="93" alt="image" src="https://github.com/user-attachments/assets/5bcb5a27-26f3-4b43-84df-8deb61cc18c2" />

## Kubernetes services 
<img width="191" height="89" alt="image" src="https://github.com/user-attachments/assets/73da432e-bbae-460e-a3e2-c9e0455e31cf" />

## deployments.png
<img width="189" height="80" alt="image" src="https://github.com/user-attachments/assets/60f96d09-ab03-4b62-8765-d9c9cf200e78" />

## logs.png 
<img width="260" height="79" alt="image" src="https://github.com/user-attachments/assets/4de3d3c0-5502-494c-a1a2-04004eea98e4" />

## browser-test.png 
Browser test using Minikube IP
<img width="359" height="66" alt="image" src="https://github.com/user-attachments/assets/8796ef6a-9109-4c8c-9a1b-0b8f1d0e427c" />
---


