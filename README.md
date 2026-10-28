# Kubernetes Deployment — Multi-Node KIND Cluster

## Project Overview

Deployed a custom Dockerized Nginx website to a local, multi-node Kubernetes cluster using KIND (Kubernetes IN Docker).

The application was originally developed in Project dockerized-web-app  and reused to demonstrate Kubernetes container orchestration, scaling, self-healing, rolling updates, and service networking.

## Technologies Used

- Kubernetes
- KIND
- Docker
- kubectl
- Nginx
- YAML
- Ubuntu

## Architecture

- **Cluster:** 1 control-plane node and 2 worker nodes
- **Deployment:** nginx-deployment
- **Replicas:** 4
- **Container image:** my-nginx-website:v1
- **Service:** nginx-service (ClusterIP)
- **Application port:** 80
- **Local access:** http://localhost:8080 using kubectl port-forward

## Project Files

```text
kubernetes-deployment/
├── kind-config.yaml
├── deployment.yaml
├── service.yaml
└── README.md
```

## Deployment Instructions

### 1. Prerequisites

Install Docker, KIND, and kubectl. Ensure Docker is running.

The custom application source and Dockerfile are available in the separate Project 2 Dockerized Web Application repository.

### 2. Create the Kubernetes cluster

```bash
kind create cluster --name devops-cluster --config kind-config.yaml
kubectl get nodes
```

### 3. Build the custom Docker image

Navigate to the Project 2 application directory containing the Dockerfile:

```bash
docker build -t my-nginx-website:v1 .
```

### 4. Load the image into KIND

```bash
kind load docker-image my-nginx-website:v1 --name devops-cluster
```

### 5. Deploy the application and Service

Return to this project's directory:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

### 6. Verify the deployment

```bash
kubectl rollout status deployment/nginx-deployment
kubectl get pods -o wide
kubectl get services
```

### 7. Access the website

```bash
kubectl port-forward service/nginx-service 8080:80
```

Visit http://localhost:8080 in your browser.

## Kubernetes Features Tested

### Scaling

Scaled the application from two to four replicas:

```bash
kubectl scale deployment/nginx-deployment --replicas=4
```

### Self-Healing

Deleted a Deployment-managed Pod using:

```bash
kubectl delete pod POD_NAME
```

Kubernetes automatically created a replacement to maintain the desired replica count.

### Rolling Updates

Tested a rolling image update and verified the rollout:

```bash
kubectl rollout status deployment/nginx-deployment
```

### Configuration Drift Detection

Compared the saved Deployment manifest with the live cluster configuration:

```bash
kubectl diff -f deployment.yaml
```

### Service Networking

Created a ClusterIP Service to provide stable in-cluster access to the application Pods.


## Key Learnings

- Creating and managing multi-node Kubernetes clusters
- Writing Kubernetes Deployment and Service manifests
- Managing replica counts
- Understanding Pods, Deployments, and Services
- Testing Kubernetes self-healing
- Performing rolling updates
- Verifying application networking
- Detecting configuration drift

## Notes

To Cleanup:

To remove the local Kubernetes cluster when it is no longer needed:

```bash
kind delete cluster --name devops-cluster
```

This deletes the KIND cluster and its Kubernetes resources. It does not delete the GitHub repository or your local project files.
