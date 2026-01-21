# TaskFlow - Distributed Task Queue System

> **Project:** TaskFlow - Distributed Task Queue System  
> **Version:** 1.0  
> **Timeline:** 7 days (Week -1 DevOps Refresh)

---

## Executive Summary

**TaskFlow** is a distributed task queue system for submitting, processing, and tracking asynchronous tasks. This project serves as a learning vehicle to master the complete DevOps stack (Docker, Kubernetes, Terraform, Helm, CI/CD) in an integrated context.

### Learning Objectives
- Master multi-stage Docker builds and image optimization
- Deploy multi-service applications on Kubernetes
- Manage Azure infrastructure with Terraform
- Package and deploy with Helm
- Automate with GitHub Actions CI/CD

### Tech Stack
| Component | Technology |
|-----------|-------------|
| API | Python 3.11 + FastAPI |
| Worker | Python 3.11 |
| Frontend | Vanilla JS + Tailwind CSS + Nginx |
| Queue | Redis 7 |
| Database | PostgreSQL 15 |
| Container | Docker + Docker Compose |
| Orchestration | Kubernetes (Minikube) |
| IaC | Terraform (Azure) |
| Package Manager | Helm 3 |
| CI/CD | GitHub Actions |

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Kubernetes (Minikube)                    │
│                                                             │
│   ┌─────────────┐                                          │
│   │  Frontend   │ (Nginx serving static files)             │
│   │  Port 80    │                                          │
│   └──────┬──────┘                                          │
│          │                                                  │
│          ▼                                                  │
│   ┌─────────────┐      ┌─────────────┐      ┌───────────┐  │
│   │ API Gateway │◄────►│   Redis     │◄────►│  Workers  │  │
│   │  (FastAPI)  │      │  (Queue)    │      │ (x3 pods) │  │
│   │  Port 8000  │      │  Port 6379  │      │           │  │
│   └──────┬──────┘      └─────────────┘      └─────┬─────┘  │
│          │                                        │        │
│          └────────────────┬───────────────────────┘        │
│                           ▼                                │
│                  ┌─────────────────┐                       │
│                  │   PostgreSQL    │                       │
│                  │   Port 5432     │                       │
│                  └─────────────────┘                       │
└─────────────────────────────────────────────────────────────┘
```

### Data Flow

1. **Submit:** Client/Frontend → API → Redis Queue + PostgreSQL (task created)
2. **Process:** Worker polls Redis → Execute → Update PostgreSQL → Store result
3. **Query:** Client/Frontend → API → PostgreSQL → Return status/result
4. **Scale:** Queue depth → HPA → Scale worker pods

---

## Frontend Dashboard

A minimalist dashboard to visualize task queue status and worker health.

### Features
- Real-time task list with status indicators
- Queue depth visualization
- Worker health status
- Task submission form
- Auto-refresh every 5 seconds

### Frontend Tech Stack
| Component | Technology |
|-----------|------------|
| Framework | Vanilla JS (no framework for simplicity) |
| Styling | Tailwind CSS (CDN) |
| Server | Nginx (static files) |
| API Calls | Fetch API |

### Dashboard Structure
```
src/frontend/
├── index.html          # Main dashboard page
├── css/
│   └── styles.css      # Custom styles (minimal)
├── js/
│   └── app.js          # API calls + DOM updates
├── Dockerfile          # Nginx multi-stage build
└── nginx.conf          # Nginx configuration
```

---

## API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `POST` | `/api/v1/tasks` | Submit a new task |
| `GET` | `/api/v1/tasks/{task_id}` | Get task by ID |
| `GET` | `/api/v1/tasks` | List tasks (filters, pagination) |
| `POST` | `/api/v1/tasks/{task_id}/cancel` | Cancel a task |
| `GET` | `/health` | Liveness probe (K8s) |
| `GET` | `/ready` | Readiness probe (K8s) |

---

## Project Structure

```
taskflow/
├── .github/workflows/     # CI/CD
├── src/
│   ├── api/              # FastAPI application
│   ├── worker/           # Task worker
│   └── frontend/         # Dashboard (Nginx + static)
├── kubernetes/           # K8s manifests
├── helm/taskflow/        # Helm chart
├── terraform/            # Azure IaC
├── docker-compose.yml    # Local development
└── README.md
```

---

## 7-Day Implementation Plan

| Day | Focus | Objective |
|-----|-------|----------|
| 1 | Docker | Complete app running in Docker Compose (API + Worker + Frontend) |
| 2 | K8s Basics | Deployment with raw YAML manifests |
| 3 | K8s Advanced | HPA, Probes, Init Containers |
| 4 | Terraform | Provision Azure ACR + Storage |
| 5 | Helm | Package app as Helm chart |
| 6 | CI/CD | Complete GitHub Actions pipeline |
| 7 | Production | Observability, security, documentation |

---

## Quick Start

```bash
# Clone the repository
git clone https://github.com/MrAlphajet-Efrei/taskflow.git
cd taskflow

# Start with Docker Compose (Day 1)
docker-compose up -d

# Access the dashboard
open http://localhost:80

# Or use the API directly
curl -X POST http://localhost:8000/api/v1/tasks \
  -H "Content-Type: application/json" \
  -d '{"task_type": "test", "payload": {"message": "Hello"}}'

# Check task status
curl http://localhost:8000/api/v1/tasks/{task_id}
```

---

## License

MIT

---

**Built as part of DevOps → MLOps reconversion journey**
