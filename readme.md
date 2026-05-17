# Microservices Kubernetes Deployment using Minikube on Windows

## Overview

This project demonstrates deployment of a Node.js based microservices application on Kubernetes using Minikube on a Windows machine.

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
- Resource Limits and Requests
- Liveness and Readiness Probes
- Internal Service Communication
- Optional Ingress Configuration

---

# System Requirements

- Windows 10 or Windows 11
- Administrator Access
- Internet Connection
- Minimum 8GB RAM Recommended

---

# Software Installation

## 1. Install Docker Desktop

Download Docker Desktop:

```text id="93ebgn"
https://www.docker.com/products/docker-desktop/
```

Install Docker Desktop and enable:

- WSL2 Backend
- Kubernetes Support (Optional)

After installation, verify Docker:

Open PowerShell:

```powershell
docker --version
```

Expected output:

```text id="3mkjlwm"
Docker version xx.x.x
```

---

# 2. Install kubectl

Open PowerShell as Administrator.

Install kubectl using Chocolatey:

```powershell
choco install kubernetes-cli -y
```

Or using winget:

```powershell
winget install -e --id Kubernetes.kubectl
```

Verify installation:

```powershell
kubectl version --client
```

---

# 3. Install Minikube

Install Minikube using Chocolatey:

```powershell
choco install minikube -y
```

Or using winget:

```powershell
winget install Kubernetes.minikube
```

Verify installation:

```powershell
minikube version
```

---

# Start Minikube Cluster

Start Minikube using Docker driver:

```powershell
minikube start --driver=docker
```

Verify cluster status:

```powershell
minikube status
```

Expected output:

```text id="omx7ql"
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

---

# Enable Ingress Controller (Optional Bonus)

Enable ingress addon:

```powershell
minikube addons enable ingress
```

Verify ingress controller:

```powershell
kubectl get pods -n ingress-nginx
```

---

# Project Folder Structure

Create the following structure:

```text id="4vljlwm"
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

Open PowerShell inside project root folder.

Configure Docker environment for Minikube:

```powershell
& minikube -p minikube docker-env --shell powershell | Invoke-Expression
```

Build User Service image:

```powershell
docker build -t user-service:latest .\user-service
```

Build Product Service image:

```powershell
docker build -t product-service:latest .\product-service
```

Build Order Service image:

```powershell
docker build -t order-service:latest .\order-service
```

Build Gateway Service image:

```powershell
docker build -t gateway-service:latest .\gateway-service
```

Verify images:

```powershell
docker images
```

---

# Deploy Application to Kubernetes

## Apply Deployments

```powershell
kubectl apply -f deployments\
```

Expected output:

```text id="hjlwm0"
deployment.apps/user-service created
deployment.apps/product-service created
deployment.apps/order-service created
deployment.apps/gateway-service created
```

---

## Apply Services

```powershell
kubectl apply -f services\
```

Expected output:

```text id="jlwm42"
service/user-service created
service/product-service created
service/order-service created
service/gateway-service created
```

---

## Apply Ingress Configuration (Optional)

```powershell
kubectl apply -f ingress\
```

---

# Verify Kubernetes Resources

## Check Running Pods

```powershell
kubectl get pods
```

Expected output:

```text id="jlwm5s"
NAME                                READY   STATUS    RESTARTS   AGE
gateway-service-xxxxx              1/1     Running   0          2m
order-service-xxxxx                1/1     Running   0          2m
product-service-xxxxx              1/1     Running   0          2m
user-service-xxxxx                 1/1     Running   0          2m
```

---

## Check Services

```powershell
kubectl get svc
```

---

## Check Deployments

```powershell
kubectl get deployments
```

---

# Validate Inter-Service Communication

## Test User Service from Order Service

```powershell
kubectl exec -it deployment/order-service -- curl http://user-service:3000
```

---

## Test Product Service from Order Service

```powershell
kubectl exec -it deployment/order-service -- curl http://product-service:3001
```

---

## Test Order Service from Gateway Service

```powershell
kubectl exec -it deployment/gateway-service -- curl http://order-service:3002
```

Expected response:

```text id="jlwm9v"
Service is reachable
```

---

# Access Application using Local IP

Get Minikube IP:

```powershell
minikube ip
```

Example output:

```text id="jlwm8q"
192.168.49.2
```

---

# Expose Gateway Service using NodePort

Run:

```powershell
kubectl expose deployment gateway-service `
--type=NodePort `
--port=3003 `
--target-port=3003 `
--name=gateway-nodeport
```

Check NodePort service:

```powershell
kubectl get svc
```

Example output:

```text id="jlwm2m"
NAME               TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)
gateway-nodeport   NodePort   10.96.10.10    <none>        3003:32003/TCP
```

---

# Access Gateway Service from Browser

Open browser:

```text id="jlwm6c"
http://192.168.49.2:32003
```

Example API routes:

```text id="jlwm0h"
http://192.168.49.2:32003/api/users

http://192.168.49.2:32003/api/products

http://192.168.49.2:32003/api/orders
```

---

# Ingress Testing (Optional Bonus)

Check ingress:

```powershell
kubectl get ingress
```

Get Minikube IP:

```powershell
minikube ip
```

Example:

```text id="jlwm4n"
192.168.49.2
```

---

# Update Windows Hosts File

Open Notepad as Administrator.

Open file:

```text id="jlwm5b"
C:\Windows\System32\drivers\etc\hosts
```

Add entry:

```text id="jlwm6x"
192.168.49.2 micro.local
```

Save the file.

---

# Test Ingress Routes

Open browser:

```text id="jlwm0x"
http://micro.local/api/users

http://micro.local/api/products

http://micro.local/api/orders
```

---

# View Logs

Gateway Service logs:

```powershell
kubectl logs deployment/gateway-service
```

Order Service logs:

```powershell
kubectl logs deployment/order-service
```

User Service logs:

```powershell
kubectl logs deployment/user-service
```

---

# Troubleshooting

## Pod CrashLoopBackOff

Check logs:

```powershell
kubectl logs <pod-name>
```

Describe pod:

```powershell
kubectl describe pod <pod-name>
```

---

## ImagePullBackOff Error

Reconfigure Docker environment:

```powershell
& minikube -p minikube docker-env --shell powershell | Invoke-Expression
```

Rebuild Docker images if required.

---

## Service Not Reachable

Check services:

```powershell
kubectl get svc
```

Check endpoints:

```powershell
kubectl get endpoints
```

---

## Restart Deployment

```powershell
kubectl rollout restart deployment gateway-service
```

---

# Cleanup

Delete deployments:

```powershell
kubectl delete -f deployments\
```

Delete services:

```powershell
kubectl delete -f services\
```

Delete ingress:

```powershell
kubectl delete -f ingress\
```

Stop Minikube:

```powershell
minikube stop
```

Delete Minikube cluster:

```powershell
minikube delete
```

---

# Screenshots Required

Capture screenshots for:

| Screenshot | Command |
|---|---|
| Running Pods | kubectl get pods |
| Running Services | kubectl get svc |
| Deployments | kubectl get deployments |
| Logs | kubectl logs deployment/gateway-service |
| Local IP Browser Test | Browser using Minikube IP |
| Ingress Browser Test | Browser using micro.local |

Store screenshots inside:

```text id="jlwm7x"
screenshots\
```

---

# Expected Outcome

After successful deployment:

- All pods should be running
- Services should communicate internally
- Gateway service should be accessible using Minikube IP
- APIs should be reachable from browser
- Ingress routing should work successfully
- Health probes should pass

---

# Final Submission

Create ZIP file:

```text id="jlwm8z"
submission.zip
```

Include:

- Deployment YAML files
- Service YAML files
- Ingress YAML file
- README.md
- Screenshots

---
