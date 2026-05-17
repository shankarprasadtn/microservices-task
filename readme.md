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

```text id="qj91rq"
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

Create the following directory structure:

```text id="mjlwm1"
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

Move to project root directory.

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

Expected images:

```text id="tyh0g9"
user-service
product-service
order-service
gateway-service
```

---

# Kubernetes Deployment Configuration

Each deployment includes:

- Container image
- Resource requests and limits
- Environment variables
- Liveness probe
- Readiness probe
- Labels and selectors

Example deployment features:

```yaml
resources:
  requests:
    memory: "128Mi"
    cpu: "100m"
  limits:
    memory: "256Mi"
    cpu: "200m"
```

Example health probes:

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 3000

readinessProbe:
  httpGet:
    path: /
    port: 3000
```

---

# Deploy Application to Kubernetes

## Apply Deployments

```bash
kubectl apply -f deployments/
```

Expected output:

```text id="v9qup2"
deployment.apps/user-service created
deployment.apps/product-service created
deployment.apps/order-service created
deployment.apps/gateway-service created
```

---

## Apply Services

```bash
kubectl apply -f services/
```

Expected output:

```text id="mn06gf"
service/user-service created
service/product-service created
service/order-service created
service/gateway-service created
```

---

## Apply Ingress (Optional)

```bash
kubectl apply -f ingress/
```

Verify ingress:

```bash
kubectl get ingress
```

---

# Verify Kubernetes Resources

## Check Pods

```bash
kubectl get pods
```

Expected output:

```text id="9y0vab"
NAME                                READY   STATUS    RESTARTS   AGE
gateway-service-xxxx               1/1     Running   0          1m
order-service-xxxx                 1/1     Running   0          1m
product-service-xxxx               1/1     Running   0          1m
user-service-xxxx                  1/1     Running   0          1m
```

---

## Check Services

```bash
kubectl get svc
```

Expected output:

```text id="q2lws0"
NAME              TYPE        CLUSTER-IP      PORT(S)
gateway-service   ClusterIP   xxx.xxx.xxx.xxx 3003/TCP
order-service     ClusterIP   xxx.xxx.xxx.xxx 3002/TCP
product-service   ClusterIP   xxx.xxx.xxx.xxx 3001/TCP
user-service      ClusterIP   xxx.xxx.xxx.xxx 3000/TCP
```

---

## Check Deployments

```bash
kubectl get deployments
```

---

# Validate Inter-Service Communication

Kubernetes service discovery works using service names.

Example:

```text id="n8zk9l"
http://user-service:3000
```

---

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

Expected response:

```text id="q91f6i"
Service is reachable
```

---

# View Application Logs

## Gateway Service Logs

```bash
kubectl logs deployment/gateway-service
```

---

## Order Service Logs

```bash
kubectl logs deployment/order-service
```

---

## User Service Logs

```bash
kubectl logs deployment/user-service
```

---

# Port Forward Testing

Expose Gateway Service locally:

```bash
kubectl port-forward svc/gateway-service 3003:3003
```

Open browser:

```text id="jlwm5d"
http://localhost:3003
```

Or test using curl:

```bash
curl http://localhost:3003
```

---

# Ingress Configuration Testing (Optional)

Get Minikube IP:

```bash
minikube ip
```

Example:

```text id="nyr7mn"
192.168.49.2
```

Edit hosts file.

Linux/macOS:

```bash
sudo nano /etc/hosts
```

Windows:

```text id="2u4x6j"
C:\Windows\System32\drivers\etc\hosts
```

Add entry:

```text id="91rjbb"
192.168.49.2 micro.local
```

Test ingress routes:

```text id="vh22q7"
http://micro.local/api/users
http://micro.local/api/products
http://micro.local/api/orders
```

---

# Troubleshooting

## Pod CrashLoopBackOff

Check logs:

```bash
kubectl logs <pod-name>
```

Describe pod:

```bash
kubectl describe pod <pod-name>
```

---

## ImagePullBackOff Error

Ensure images are built inside Minikube Docker environment:

```bash
eval $(minikube docker-env)
```

Rebuild images if required.

---

## Service Not Reachable

Check services:

```bash
kubectl get svc
```

Check endpoints:

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

# Screenshots Required

Capture the following screenshots:

| Screenshot | Command |
|---|---|
| Running Pods | kubectl get pods |
| Running Services | kubectl get svc |
| Logs | kubectl logs deployment/gateway-service |
| Port Forward Test | Browser or curl output |
| Ingress Test | Browser output |

Store screenshots inside:

```text id="fdtvxj"
screenshots/
```

---

# Expected Outcome

After successful deployment:

- All pods should be running
- Services should communicate internally
- Gateway should route requests correctly
- Health checks should pass
- Ingress routing should work properly
- No pod crashes or networking issues

---

# Submission

Create final ZIP file:

```text id="4d6d4u"
submission.zip
```

Include:

- Deployment YAML files
- Service YAML files
- Ingress YAML file
- README.md
- Screenshots

---