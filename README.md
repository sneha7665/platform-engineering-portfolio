# 🏗️ Internal Developer Platform (IDP) with Backstage

> **Platform Engineering Project — Automating microservice scaffolding to reduce developer onboarding time from days to minutes.**

## 📌 Problem Statement

In most engineering teams, creating a new microservice involves repetitive, error-prone manual work:

* Creating a GitHub repository with branch protection rules
* Writing a Dockerfile from scratch
* Setting up CI/CD pipelines
* Writing Kubernetes deployment manifests
* Adding health probes, resource limits, and monitoring
* Writing documentation and READMEs

This takes **2–3 days per service** and leads to:

* ❌ Inconsistent standards across teams
* ❌ Security misconfigurations
* ❌ Duplicated boilerplate code
* ❌ High cognitive load on developers

## 💡 Solution

Built an **Internal Developer Platform (IDP)** using **Backstage**, Spotify's open-source platform framework, that provides a **Golden Path** for creating production-ready Node.js microservices.

### What is a Golden Path?

> **"The blessed, supported way to build something."**

A Golden Path is a standardized, secure, and automated template that developers can use to create services consistently.

---

## ⚡ Developer Experience: Before vs After

| Metric             | Before IDP         | After IDP                  |
| ------------------ | ------------------ | -------------------------- |
| Service setup time | 2–3 days           | **30 seconds**             |
| Standardization    | Inconsistent       | **Enforced via templates** |
| CI/CD setup        | Manual             | **Auto-generated**         |
| Security scanning  | Optional / Ad-hoc  | **Built into pipeline**    |
| Catalog visibility | Spreadsheet / None | **Auto-registered**        |

---

## 🏛️ Architecture

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                           DEVELOPER EXPERIENCE                               │
│                                                                             │
│   ┌──────────────┐         Fill Form          ┌──────────────────────┐     │
│   │  Developer   │ ─────────────────────────▶ │   Backstage Portal   │     │
│   │  (Any Team)  │                             │ Internal Developer   │     │
│   └──────────────┘                             │      Platform        │     │
│                                                └──────────┬───────────┘     │
└───────────────────────────────────────────────────────────┼─────────────────┘
                                                            │
                                                            ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         SCAFFOLDER ENGINE (Automation)                      │
│                                                                             │
│   ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────────┐   │
│   │ 1. Fetch Base   │───▶│ 2. Publish to   │───▶│ 3. Register in      │   │
│   │    Template     │    │    GitHub       │    │    Catalog          │   │
│   │                 │    │                 │    │                     │   │
│   │ • Skeleton      │    │ • Create repo   │    │ • catalog-info.yaml │   │
│   │ • Variables     │    │ • Replace vars  │    │ • Auto-discover     │   │
│   │ • Dockerfile    │    │ • Push files    │    │ • Track ownership   │   │
│   │ • CI/CD YAML    │    │ • Set branch    │    │ • Link to source    │   │
│   └─────────────────┘    └─────────────────┘    └─────────────────────┘   │
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
│   │   └── service.yaml         → ClusterIP service                          │
│   └── 📄 catalog-info.yaml     → Backstage metadata for discovery           │
└─────────────────────────────────────────────────────────────────────────────┘
                                                            │
                                                            ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         CONTAINER ORCHESTRATION (K8s)                       │
│                                                                             │
│   ┌─────────────────────┐         ┌─────────────────────────────────────┐   │
│   │   Docker Build      │────────▶│   Minikube (Local K8s Cluster)      │   │
│   │   auth-service:latest│        │                                     │   │
│   └─────────────────────┘         │   ┌─────────────────────────────┐   │   │
│                                   │   │ Deployment (2 replicas)     │   │   │
│                                   │   │ • Liveness Probe: /health   │   │   │
│                                   │   │ • Readiness Probe: /health  │   │   │
│                                   │   │ • Resource Limits: 256Mi    │   │   │
│                                   │   │ • Resource Requests: 128Mi  │   │   │
│                                   │   └─────────────────────────────┘   │   │
│                                   │   ┌─────────────────────────────┐   │   │
│                                   │   │ Service (ClusterIP)         │   │   │
│                                   │   │ Port: 80 → Target: 3000     │   │   │
│                                   │   └─────────────────────────────┘   │   │
│                                   └─────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
                                                            │
                                                            ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           LIVE APPLICATION                                  │
│                                                                             │
│   $ curl http://localhost:8080                                             │
│   {"service":"auth-service","status":"running"}                            │
│                                                                             │
│   $ curl http://localhost:8080/health                                       │
│   {"status":"healthy"}                                                     │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Layer            | Technology            | Purpose                                      |
| ---------------- | --------------------- | -------------------------------------------- |
| Portal Framework | Backstage             | Internal Developer Platform                  |
| Scaffolding      | Backstage Scaffolder  | Template engine & workflow runner            |
| Source Control   | GitHub                | Repository hosting & GitHub Actions          |
| CI/CD            | GitHub Actions        | Automated build, test, and security scanning |
| Containerization | Docker                | Application packaging                        |
| Orchestration    | Kubernetes (Minikube) | Local container orchestration                |
| Language         | Node.js 18 + Express  | Microservice runtime                         |
| Configuration    | YAML                  | Templates, K8s manifests & pipelines         |

---

## 📸 Project Screenshots

### 1. Backstage Templates Gallery

The portal homepage showing the custom **Node.js Microservice Golden Path** template alongside the default example.

### 2. Scaffolder Task Execution Log

Real-time automation log showing all three steps completing successfully:

**Fetch Template → Publish to GitHub → Register in Catalog**

Total execution time: **9 seconds**.

### 3. Auto-Generated GitHub Repository

The `auth-service` repository created automatically by Backstage.

The commit author is **"Scaffolder"**, demonstrating that the repository was generated through automation rather than manually pushed.

### 4. Service Detail in Backstage Catalog

The `auth-service` entity page showing:

* Lifecycle: production
* Owner: my-team
* Type: service
* Direct source-code links

### 5. Centralized Service Catalog

All services are displayed in one centralized catalog. As teams scaffold new services through the portal, they are automatically registered for discovery.

### 6. Kubernetes Deployment Terminal

Shows:

* Docker build success
* `kubectl apply`
* Running pods with `1/1 READY`
* Port-forwarding to access the service locally

### 7. Live Application Response

The running microservice responds to HTTP requests.

Health endpoint:

```json
{
  "status": "healthy"
}
```

This endpoint is also used by Kubernetes liveness and readiness probes.

### 8. GitHub Actions CI/CD Pipeline

Automated pipeline triggered on every push:

```text
Checkout
   ↓
Setup Node.js
   ↓
Install Dependencies
   ↓
Run Tests
   ↓
Build Docker Image
   ↓
Security Scan
```

---

## 🚀 How It Works

### For the Developer — The "Golden Path"

```text
Open Backstage Portal
        ↓
Navigate to Create
        ↓
Select "Node.js Microservice"
        ↓
Fill Form
(Service Name, Owner, Repository Location)
        ↓
Click Create
        ↓
Backstage Scaffolder executes template
        ↓
GitHub Repository automatically created
        ↓
GitHub Actions CI/CD starts automatically
        ↓
Service automatically registered in Backstage Catalog
```

### What Happens Under the Hood

The Backstage template defines the complete workflow:

```yaml
# template.yaml — The Blueprint

spec:
  steps:

    - id: fetch-base
      action: fetch:template
      input:
        url: ./skeleton
        values:
          name: ${{ parameters.name }}

    - id: publish
      action: publish:github
      input:
        repoUrl: ${{ parameters.repoUrl }}

    - id: register
      action: catalog:register
      input:
        repoContentsUrl: ${{ steps.publish.output.repoContentsUrl }}
```

### Template Workflow

| Step | Action             | Purpose                                  |
| ---- | ------------------ | ---------------------------------------- |
| 1    | `fetch:template`   | Downloads the skeleton template          |
| 2    | `publish:github`   | Creates and pushes the GitHub repository |
| 3    | `catalog:register` | Registers the service in Backstage       |

---

## 🔄 Variable Replacement

The template engine dynamically replaces placeholders in the skeleton files.

| Skeleton File       | Placeholder          | Replaced With         |
| ------------------- | -------------------- | --------------------- |
| `package.json`      | `${{ values.name }}` | `auth-service`        |
| `index.js`          | `${{ values.name }}` | `auth-service`        |
| `Dockerfile`        | `${{ values.name }}` | `auth-service:latest` |
| `catalog-info.yaml` | `${{ values.name }}` | `auth-service`        |

This allows the same Golden Path template to generate multiple standardized microservices.

---

## 🔐 Security & Production Best Practices

Every scaffolded service includes:

* ✅ Vulnerability scanning with **Trivy**
* ✅ Kubernetes resource limits and requests
* ✅ Liveness and readiness probes
* ✅ Minimal Node.js Alpine base image
* ✅ Least-privilege GitHub tokens
* ✅ `/health` endpoint for Kubernetes orchestration
* ✅ Standardized CI/CD pipeline

---

## 📊 Project Impact

| Before Platform Engineering            | After Platform Engineering     |
| -------------------------------------- | ------------------------------ |
| 2–3 days to scaffold a service         | **30 seconds**                 |
| Inconsistent Docker/K8s configurations | **Standardized Golden Path**   |
| Manual CI/CD setup                     | **Auto-generated pipeline**    |
| No central service registry            | **Auto-registered catalog**    |
| Ad-hoc security practices              | **Built-in security scanning** |

---

## 🎯 Key Concepts Demonstrated

| Concept                       | How This Project Demonstrates It                           |
| ----------------------------- | ---------------------------------------------------------- |
| **Platform Engineering**      | Built internal tooling to reduce developer cognitive load  |
| **Golden Path**               | Standardized and supported template for service creation   |
| **Developer Experience (DX)** | One form → production-ready service                        |
| **Software Catalog**          | Centralized registry for service discovery                 |
| **GitOps**                    | Declarative templates stored in Git and version controlled |
| **Shift-Left Security**       | Vulnerability scanning integrated into CI                  |
| **Infrastructure as Code**    | Kubernetes manifests and CI/CD defined declaratively       |

---

## 🔮 Future Enhancements

* [ ] **ArgoCD Integration** — True GitOps: push code → automatically deploy to Kubernetes
* [ ] **Prometheus + Grafana** — Auto-generated monitoring dashboards
* [ ] **Policy-as-Code** — Kyverno/OPA for Kubernetes governance
* [ ] **Cost Visibility** — Estimated cloud spend per service
* [ ] **Service Maturity Score** — Track security and observability compliance
* [ ] **Multi-Cloud Deployment** — AWS EKS, GCP GKE and Azure AKS templates
* [ ] **Custom Backstage Plugins** — Jira, PagerDuty and company-specific integrations

---

## 🏃 Quick Start

### Prerequisites

Make sure you have the following installed:

* Node.js
* Yarn
* Docker
* Kubernetes / Minikube
* kubectl
* GitHub account with appropriate repository permissions

### 1. Clone the Backstage Application

```bash
cd ~/platform-portal/platform-portal
```

### 2. Configure GitHub Token

```bash
export GITHUB_TOKEN="ghp_your_token_here"
```

> ⚠️ Never commit your GitHub token to Git or expose it publicly.

### 3. Start the Backstage Portal

```bash
yarn start
```

### 4. Open Backstage

Open:

```text
http://localhost:3000
```

Then navigate to:

```text
Create → Node.js Microservice → Fill Form → Create
```

### 5. Deploy to Local Kubernetes

Start Minikube:

```bash
minikube start --driver=docker
```

Configure Docker to use Minikube:

```bash
eval $(minikube docker-env)
```

Build the generated service:

```bash
cd ~/auth-service

docker build -t auth-service:latest .
```

Deploy to Kubernetes:

```bash
kubectl apply -f k8s/
```

Port-forward the service:

```bash
kubectl port-forward svc/auth-service 8080:80
```

Test the application:

```bash
curl http://localhost:8080
```

Health check:

```bash
curl http://localhost:8080/health
```

Expected response:

```json
{
  "status": "healthy"
}
```

---

## 📚 What I Learned

Through this project, I gained practical experience with:

* Platform Engineering principles
* Developer Experience (DX)
* Backstage architecture
* Backstage frontend and backend concepts
* Backstage plugin system
* Backstage Scaffolder
* Software Templates
* Catalog-driven development
* GitHub Actions CI/CD
* Container security scanning
* Docker containerization
* Kubernetes Deployments and Services
* Kubernetes health probes
* Kubernetes resource management
* Infrastructure as Code
* GitOps concepts
* Shift-left security

### Iteration & Problem Solving

One important lesson was the value of iteration based on real usage.

The initial template used `npm ci`, which failed when used with fresh repositories. After testing and troubleshooting, it was changed to `npm install` to make the generated repositories work reliably.

---

## 👩‍💻 Author

**Sneha**

**Project Type:** Platform Engineering / Internal Developer Platform

**Status:** ✅ Complete & Demo-Ready
