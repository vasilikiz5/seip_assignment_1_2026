# Software Engineering in Practice — Assignment 1 (2026)
## Advanced DevOps: Production-Grade CI/CD, External Configuration, and Orchestration

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


**AI Integration**
I used Claude to help me understand how Kubernetes works, how YAML syntax is formed and how to overcome some errors I encountered during the setup process.

**Utility Analysis**
The AI was helpful for:
* Explaining error messages from Github Actions and Minikube
* Quickly identifying syntax issues in YAML files
* Understanding the Base64 encoding process and the newline pitfall when creating Secrets
* Clarifying the relationship between Kubernetes components fast

**Friction Points**
* The initial GitHub Actions workflow was missing the permissions 
  block for GHCR, which caused the pipeline to fail. I had to add "packages: write" to fix it
* Minikube defaulted to the VirtualBox driver instead of Docker, requiring to manually specify "--driver=docker"
* Docker Desktop could not start because I had previously disabled WSL2 and Virtual Machine Platform in order to run Oracle VirtualBox for another course. These two Windows features conflict with VirtualBox, so I had to re-enable them and restart my machine before Docker Desktop would launch

**Future Architectural Outlook**
If I had more time or needed to scale this project for a real 
production environment, the first thing I would change is replacing 
port-forwarding with an Ingress Controller like NGINX, since 
port-forward is only meant for local testing and not for actual 
users to access the application.

I would also look into setting up ArgoCD for GitOps, so that 
every time I push changes to GitHub, the Kubernetes cluster 
updates itself automatically without me having to run kubectl 
commands manually.

On the security side, I would integrate an image scanner like 
Trivy into the CI pipeline to catch vulnerabilities in the 
Docker image before it even gets deployed.

Finally, right now the deployment always runs exactly 3 pods 
regardless of traffic. A Horizontal Pod Autoscaler would 
automatically scale the number of pods up or down based on 
actual demand, which is both more efficient and more realistic 
for production use.
