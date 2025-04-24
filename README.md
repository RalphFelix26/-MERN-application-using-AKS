# MERN Application Deployment on Azure Kubernetes Service (AKS)

## 📄 Project Overview
This project walks through deploying a **MERN (MongoDB, Express.js, React.js, Node.js)** stack application using **Azure Kubernetes Service (AKS)**.

---

## ⚖️ Prerequisites

Ensure the following tools are installed:
- Azure CLI
- Kubectl
- Docker
- Git

---

## 🌐 Step 1: Azure Setup

### 1.1 Login to Azure
```bash
az login
```

### 1.2 Create a Resource Group
```bash
az group create --name mern-rg --location eastus
```

### 1.3 Create AKS Cluster
```bash
az aks create \
  --resource-group mern-rg \
  --name mern-cluster \
  --node-count 2 \
  --enable-addons monitoring \
  --generate-ssh-keys
```

### 1.4 Get AKS Credentials
```bash
az aks get-credentials --resource-group mern-rg --name mern-cluster
```

---

## 📦 Step 2: Clone the Application
```bash
git clone https://github.com/UnpredictablePrashant/SampleMERNwithMicroservices.git
cd SampleMERNwithMicroservices
```

---

## 🛠️ Step 3: Dockerize the Application

### 3.1 Backend
```bash
cd backend
docker build -t <yourdockerhub>/mern-backend .
docker push <yourdockerhub>/mern-backend
cd ..
```

### 3.2 Frontend
```bash
cd frontend
docker build -t <yourdockerhub>/mern-frontend .
docker push <yourdockerhub>/mern-frontend
cd ..
```

---

## ⚛️ Step 4: Kubernetes Deployment Files

Create a folder `k8s/` and add the following files:

### backend-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend
        image: <yourdockerhub>/mern-backend
        ports:
        - containerPort: 3001
```

### backend-service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
    - protocol: TCP
      port: 3001
      targetPort: 3001
```

### frontend-deployment.yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: <yourdockerhub>/mern-frontend
        ports:
        - containerPort: 3000
```

### frontend-service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  type: LoadBalancer
  selector:
    app: frontend
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
```

---

## 🚀 Step 5: Deploy to AKS

```bash
kubectl apply -f k8s/
```

Check services:
```bash
kubectl get svc
```

Access the application via the EXTERNAL-IP of `frontend-service`:
```bash
http://<EXTERNAL-IP>
```

---

## 🖊️ Documentation Checklist

### Include the following:
- Azure Portal: Resource Group and AKS Cluster screenshots
- Docker Hub: Images pushed
- Kubernetes CLI: Running Pods and Services
- Browser: Application working

---

## 🚮 Clean Up Resources
```bash
az group delete --name mern-rg
```

---

## 🗂️ Folder Structure
```
SampleMERNwithMicroservices/
├── backend/
│   └── Dockerfile
├── frontend/
│   └── Dockerfile
└── k8s/
    ├── backend-deployment.yaml
    ├── backend-service.yaml
    ├── frontend-deployment.yaml
    └── frontend-service.yaml
```

---

## 🎉 You're Done!
Your MERN app is now deployed on Azure Kubernetes Service!
