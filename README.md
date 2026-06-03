# Software Engineering in Practice — Assignment 1 (2026)
## Advanced DevOps: Production-Grade CI/CD, External Configuration, and Orchestration

## Overview 
This project takes a Node.js web application and builds the infrastructure around it. It packages it in a Docker container, automates the build and push proccess to GitHub Container Registry through Github Actions. The application is deployed it on a local Kubernetes cluster using Minikube and reads its configuration from Kubernetes ConfigMaps and Secrets, keeping sensitive data seperate from teh code.

## Prerequisites
* Git
* Docker Desktop
* kubectl
* Minikube

**Step 1: Clone the Repository**

```bash
git clone https://github.com/vasilikiz5/seip_assignment_1_2026
cd seip_assignment_1_2026
```

**Step 2: Start Minikube**

```bash
minikube start --driver=docker
```

**Step 3: Apply Kubernetes Configuration Files**

```bash
kubectl apply -f k8s/
```

**Step 4: Verify the Cluster**

```bash
kubectl get all -n default
kubectl get configmap,secret
```

You should see 3 running pods, the deployment, and the ClusterIP service.

**Step 5: Access the Application**

```bash
kubectl port-forward service/echo-api-service 8080:80
```

Then open your browser:

- http://localhost:8080/ — Shows the welcome message and environment
- http://localhost:8080/secure-config — Shows "Authorized" and masked API key
- http://localhost:8080/health — Shows "Healthy"

**Step 6: Application Endpoints**
The application exposes three endpoints:

* The root endpoint (`/`) returns the welcome message configured in the ConfigMap along with the current environment (production).

* The secure config endpoint (`secure-config`) verifies that the API secret key was injected correctly from the Kubernetes Secret. It returns an "Authorized" status and a masked version of the key, showing only the last four characters.

* The health endpoint (`/health`) returns a simple "Health" status. This is used by the Kubernetes liveness and readiness probes to check if the application is runnign and ready to accept traffic.
