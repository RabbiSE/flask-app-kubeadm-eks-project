# Two-Tier Flask App Deployment Guide

---

## Docker Compose

### Prerequisites
- Open port `5000` in EC2 security group
- Create `mysql-data` directory for volume

```bash
mkdir mysql-data
```

### Build & Run

```bash
sudo docker build . -t flask-app
```

### Docker Hub

```bash
docker login -u rabbise
```
> Authenticate with generated token

```bash
sudo docker build -t rabbise/two-tier-flask-app:latest .
docker push rabbise/two-tier-flask-app:latest
```

### Start Services

```bash
sudo docker-compose up -d
```

---

## Kubeadm

### Prerequisites
- Open ports `6443`, `30004`, and `10250`
- Create `mysqldata` directory for volume

```bash
mkdir mysqldata
```

### Deploy

```bash
kubectl apply -f mysql-pv.yml
kubectl apply -f mysql-pvc.yml
kubectl apply -f mysql-deployment.yml
kubectl apply -f mysql-svc.yml
kubectl get svc  # Check MySQL cluster IP
kubectl apply -f two-tier-app-deployment.yml
kubectl apply -f two-tier-app-svc.yml
```

> **Access:** `<Worker IP>:<NodePort>`

---

## Helm

### Setup

```bash
mkdir Helm
```

### MySQL Chart

```bash
helm create mysql-chart
helm package mysql-chart/
helm install mysql-chart ./mysql-chart
kubectl get svc  # Check MySQL cluster IP
```

### Flask App Chart

```bash
helm create flask-app-chart
helm package flask-app-chart/
helm install flask-app-chart ./flask-app-chart
```

---

## EKS

### Deploy MySQL

```bash
kubectl apply -f mysql-deployment.yml -f mysql-svc.yml -f mysql-secrets.yml -f mysql-configmap.yml
kubectl get svc  # Check MySQL cluster IP
```

### Deploy Flask App

```bash
kubectl apply -f two-tier-app-deployment.yml -f two-tier-app-svc.yml
```
