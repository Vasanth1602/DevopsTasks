# Deploying a Static HTML Page in Kubernetes - Beginner DevOps Task

## 📋 Task Overview
This project demonstrates how to **deploy a simple static web page using Kubernetes**. As a beginner-level DevOps task, the goal was to understand the end-to-end flow of containerizing a web page and serving it through Kubernetes.

## 🎯 Objective:
- Containerize a simple static HTML page using **Docker**.
- Deploy the containerized application on **Kubernetes** using **Deployment** and **Service** YAML configurations.
- Expose the application using a **NodePort Service** to access it externally.

---

##  Project Structure
```
.
├── Dockerfile
├── index.html
├── deployment.yaml
└── service.yaml
```

## Containerization 
- Built your Docker image using: ```docker build -t static-website .```
- Start the contaier using: ```docker run -d -p 8080:80 --name mycontainer static-website```
  
## Deployment
- Apply the deployment configuration ```kubectl apply -f deployment.yaml```
- Apply the service configuration ```kubectl apply -f service.yaml```
- Check the Deployment and service using: ```kubectl get pods``` and ```kubectl get services```