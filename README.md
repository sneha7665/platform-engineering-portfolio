🏗️ Internal Developer Platform (IDP) with Backstage
Platform Engineering Project — Automating microservice scaffolding to reduce developer onboarding time from days to minutes.
📌 Problem Statement
In most engineering teams, creating a new microservice involves repetitive, error-prone manual work:
Creating a GitHub repository with branch protection rules
Writing a Dockerfile from scratch
Setting up CI/CD pipelines
Writing Kubernetes deployment manifests
Adding health probes, resource limits, and monitoring
Writing documentation and READMEs
This takes 2–3 days per service and leads to:
❌ Inconsistent standards across teams
❌ Security misconfigurations
❌ Duplicated boilerplate code
❌ High cognitive load on developers
💡 Solution
Built an Internal Developer Platform using Backstage (Spotify's open-source platform framework) that provides a Golden Path for creating production-ready Node.js microservices.
What is a Golden Path?
"The blessed, supported way to build something." — A standardized, secure, and automated template that every developer can use with confidence.
Developer Experience (Before vs After)
Table
Metric	Before IDP	After IDP
Service setup time	2–3 days	30 seconds
Standardization	Inconsistent	Enforced via templates
CI/CD setup	Manual	Auto-generated
Security scanning	Optional/Ad-hoc	Built into pipeline
Catalog visibility	Spreadsheet/None	Auto-registered
🏛️ Architecture
plain
┌─────────────────────────────────────────────────────────────────────────────┐
│                           DEVELOPER EXPERIENCE                               │
│                                                                             │
│   ┌──────────────┐         Fill Form          ┌──────────────────────┐   │
│   │  Developer   │ ──────────────────────────▶ │   Backstage Portal   │   │
│   │  (Any Team)  │                             │  (Internal Developer │   │
│   └──────────────┘                             │       Platform)      │   │
│                                                └──────────┬───────────┘   │
│                                                           │               │
└───────────────────────────────────────────────────────────┼───────────────┘
                                                            │
                                                            ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         SCAFFOLDER ENGINE (Automation)                     │
│                                                                             │
│   ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────────┐    │
│   │  1. Fetch Base  │───▶│ 2. Publish to   │───▶│ 3. Register in      │    │
│   │     Template    │    │    GitHub       │    │    Catalog          │    │
│   │                 │    │                 │    │                     │    │
│   │  • Skeleton     │    │  • Create repo  │    │  • catalog-info.yaml│    │
│   │  • Variables    │    │  • Replace vars │    │  • Auto-discover  │    │
│   │  • Dockerfile   │    │  • Push files   │    │  • Track ownership  │    │
│   │  • CI/CD YAML │    │  • Set branch   │    │  • Link to source   │    │
│   └─────────────────┘    └─────────────────┘    └─────────────────────┘    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
                                                            │
                                                            ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         AUTO-GENERATED REPOSITORY                            │
│                                                                             │
│   📁 auth-service/                                                          │
│   ├── 📄 index.js              → Express app with /health endpoint          │
│   ├── 📄 package.json          → Dependencies & scripts                     │
│   ├── 📄 Dockerfile            → Multi-stage Node.js image (Alpine)         │
│   ├── 📁 .github/workflows/                                                  │
│   │   └── ci.yml               → GitHub Actions: test + build + scan       │
│   ├── 📁 k8s/                                                                 │
│   │   ├── deployment.yaml      → Deployment with probes & resource limits │
│   │   └── service.yaml         → ClusterIP service                        │
│   └── 📄 catalog-info.yaml     → Backstage metadata for discovery         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
                                                            │
                                                            ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         CONTAINER ORCHESTRATION (K8s)                      │
│                                                                             │
│   ┌─────────────────────┐         ┌─────────────────────────────────────┐  │
│   │   Docker Build      │────────▶│   Minikube (Local K8s Cluster)      │  │
│   │   auth-service:latest│       │                                     │  │
│   └─────────────────────┘         │   ┌─────────────────────────────┐   │  │
│                                   │   │  Deployment (2 replicas)     │   │  │
│                                   │   │  • Liveness Probe: /health   │   │  │
│                                   │   │  • Readiness Probe: /health  │   │  │
│                                   │   │  • Resource Limits: 256Mi    │   │  │
│                                   │   │  • Resource Requests: 128Mi  │   │  │
│                                   │   └─────────────────────────────┘   │  │
│                                   │   ┌─────────────────────────────┐   │  │
│                                   │   │  Service (ClusterIP)         │   │  │
│                                   │   │  Port: 80 → Target: 3000    │   │  │
│                                   │   └─────────────────────────────┘   │  │
│                                   └─────────────────────────────────────┘  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
                                                            │
                                                            ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         LIVE APPLICATION                                     │
│                                                                             │
│   $ curl http://localhost:8080                                             │
│   {"service":"auth-service","status":"running"}                           │
│                                                                             │
│   $ curl http://localhost:8080/health                                       │
│   {"status":"healthy"}                                                     │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
🛠️ Tech Stack
Table
Layer	Technology	Purpose
Portal Framework	Backstage (Spotify)	Internal Developer Platform
Scaffolding	Backstage Scaffolder	Template engine & workflow runner
Source Control	GitHub	Repo hosting & GitHub Actions
CI/CD	GitHub Actions	Automated build, test, and scan
Containerization	Docker	Application packaging
Orchestration	Kubernetes (Minikube)	Local container orchestration
Language	Node.js 18 + Express	Microservice runtime
Config	YAML	Templates, K8s manifests, pipelines
📸 Project Screenshots
1. Backstage Templates Gallery
The portal homepage showing the custom "Node.js Microservice" Golden Path template alongside the default example.
2. Scaffolder Task Execution Log
Real-time automation log showing all 3 steps completing successfully: Fetch Template → Publish to GitHub → Register in Catalog (total: 9 seconds).
3. Auto-Generated GitHub Repository
The auth-service repository created automatically by Backstage. Notice the commit author is "Scaffolder" — proof of automation, not manual push.
4. Service Detail in Backstage Catalog
The auth-service entity page showing lifecycle (production), owner (my-team), type (service), and direct links to source code.
5. Centralized Service Catalog
All company services in one view. As teams scaffold new services through the portal, they appear here automatically for discovery.
6. Kubernetes Deployment Terminal
Docker build success, kubectl apply, and pods running with 1/1 READY status. Includes port-forward to access the service locally.
7. Live Application Response
The running microservice responding to HTTP requests. Health endpoint returns {"status":"healthy"} — used by Kubernetes probes.
8. GitHub Actions CI/CD Pipeline (Green)
Automated pipeline running on every push: checkout → setup Node → install dependencies → run tests → build Docker image.
🚀 How It Works (Step-by-Step)
For the Developer (The "Golden Path")
Open Backstage Portal → Navigate to Create → Select Node.js Microservice
Fill the Form → Service Name, Owner, Repository Location
Click Create → Backstage Scaffolder executes the template workflow
Get a Repo → GitHub repository auto-created with all production files
CI/CD Runs → GitHub Actions triggers automatically on the first commit
Find in Catalog → Service is auto-registered and discoverable by any team
What Happens Under the Hood
yaml
# template.yaml — The Blueprint
spec:
  steps:
    - id: fetch-base
      action: fetch:template        # Download skeleton files
      input:
        url: ./skeleton
        values:
          name: ${{ parameters.name }}   # Replace variables

    - id: publish
      action: publish:github        # Create & push repo
      input:
        repoUrl: ${{ parameters.repoUrl }}

    - id: register
      action: catalog:register      # Add to Backstage catalog
      input:
        repoContentsUrl: ${{ steps.publish.output.repoContentsUrl }}
Variable Replacement in Skeleton Files
The template engine replaces placeholders in every file:
Table
Skeleton File	Placeholder	Replaced With
package.json	"name": "${{ values.name }}"	"auth-service"
index.js	service: '${{ values.name }}'	service: 'auth-service'
Dockerfile	image: ${{ values.name }}:latest	auth-service:latest
catalog-info.yaml	name: ${{ values.name }}	name: auth-service
🔐 Security & Production Best Practices Built In
Every scaffolded service includes:
✅ Vulnerability scanning via Trivy (in CI pipeline)
✅ Resource limits & requests in Kubernetes (prevents noisy neighbors)
✅ Liveness & readiness probes (ensures reliable deployments)
✅ Minimal base image (Node.js Alpine — reduces attack surface)
✅ Least-privilege GitHub tokens (PAT with minimal scopes)
✅ Health endpoint (/health) for K8s orchestration
📊 Impact
Table
Before Platform Engineering	After Platform Engineering
2–3 days to scaffold a service	30 seconds
Inconsistent Docker/K8s configs	Standardized Golden Path
Manual CI/CD setup per repo	Auto-generated pipeline
No central service registry	Auto-registered catalog
Ad-hoc security practices	Built-in security scanning
🎯 Key Concepts Demonstrated
Table
Concept	How This Project Shows It
Platform Engineering	Built internal tooling to reduce developer cognitive load
Golden Path	Standardized, supported template for service creation
Developer Experience (DX)	One form → production-ready service in 30 seconds
Software Catalog	Centralized registry for service discovery
GitOps	Declarative templates stored in Git, version controlled
Shift-Left Security	Vulnerability scanning in CI from day one
Infrastructure as Code	K8s manifests and CI/CD defined declaratively
🔮 Future Enhancements
[ ] ArgoCD Integration — True GitOps: push code → auto-deploy to K8s
[ ] Prometheus + Grafana — Auto-generated monitoring dashboards per service
[ ] Policy-as-Code (Kyverno/OPA) — Enforce security rules on K8s manifests
[ ] Cost Visibility — Show estimated cloud spend per service
[ ] Service Maturity Score — Track compliance with security/observability standards
[ ] Multi-Cloud Deployment — Template variants for AWS EKS, GCP GKE, Azure AKS
[ ] Custom Backstage Plugins — Company-specific integrations (Jira, PagerDuty, etc.)
🏃 Quick Start (To Run Locally)
bash
# 1. Clone the Backstage app
cd ~/platform-portal/platform-portal

# 2. Set your GitHub token
export GITHUB_TOKEN="ghp_your_token_here"

# 3. Start the portal
yarn start

# 4. Open http://localhost:3000
# 5. Go to Create → Node.js Microservice → Fill form → Create

# 6. (Optional) Deploy to local K8s
minikube start --driver=docker
eval $(minikube docker-env)
cd ~/auth-service
docker build -t auth-service:latest .
kubectl apply -f k8s/
kubectl port-forward svc/auth-service 8080:80
📚 What I Learned
Platform Engineering principles and the importance of Developer Experience (DX)
Backstage architecture: frontend (React), backend (Node.js), plugin system, Scaffolder, Catalog
Software Templates and catalog-driven development
CI/CD best practices with GitHub Actions and container security scanning
Kubernetes fundamentals: Deployments, Services, Probes, Resource Management
The value of iteration: Initial template used npm ci which failed on fresh repos; switched to npm install based on real usage feedback
Built by: Sneha
Project Type: Platform Engineering / Internal Developer Platform
Status: ✅ Complete & Demo-Ready
