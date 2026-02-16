

# Eximso Automation

This repository contains **automation workflows for Eximso**, built using **n8n**.
It serves as the single source of truth for workflow definitions, Docker setup, and CI/CD pipelines that deploy changes automatically to AWS.

---

## 📦 Repository Purpose

* Store **n8n workflow JSON files**
* Provide a **Docker-based setup** for running n8n with predefined workflows
* Use **GitHub Actions** to automatically publish workflow changes to **AWS**
* Enable consistent, repeatable automation deployments across environments

---

## 🧠 Tech Stack

* **n8n** – Workflow automation engine
* **Docker** – Containerized runtime for n8n
* **GitHub Actions** – CI/CD automation
* **AWS** – Deployment target (ECR / ECS / EC2 depending on environment)

---

## 📁 Repository Structure

```text
eximso-automation/
│
├── workflows/
│   ├── order-sync.json
│   ├── invoice-trigger.json
│   └── ...
│
├── docker/
│   ├── Dockerfile
│   └── entrypoint.sh
│
├── .github/
│   └── workflows/
│       └── deploy.yml
│
├── .env.example
├── README.md
└── .gitignore
```

---

## 🔄 Workflows

All n8n workflows live inside the `workflows/` directory.

### Rules

* Each workflow is stored as a **JSON export from n8n**
* File names should be **kebab-case** and descriptive
* One workflow per file

Example:

```text
workflows/
├── user-onboarding.json
├── payment-retry-handler.json
```

---

## 🐳 Docker Setup

The Docker setup allows running n8n with preloaded workflows.

### Dockerfile Responsibilities

* Install n8n
* Copy workflow JSON files into the container
* Configure environment variables
* Start n8n automatically

### Example (high-level)

```dockerfile
FROM n8nio/n8n

COPY workflows /workflows
COPY docker/entrypoint.sh /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]
```

---

## ▶️ Running Locally

### 1. Create environment file

```bash
cp .env.example .env
```

Update values as needed.

### 2. Build and run

```bash
docker build -t eximso-n8n .
docker run -p 5678:5678 --env-file .env eximso-n8n
```

n8n will be available at:

```
http://localhost:5678
```

---

## 🔐 Environment Variables

Key variables used by n8n:

```env
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=securepassword

N8N_HOST=localhost
N8N_PORT=5678
N8N_PROTOCOL=http

DB_TYPE=postgresdb
DB_POSTGRESDB_HOST=
DB_POSTGRESDB_DATABASE=
DB_POSTGRESDB_USER=
DB_POSTGRESDB_PASSWORD=
```

> ⚠️ Never commit real secrets. Use GitHub Secrets for production.

---

## 🚀 CI/CD with GitHub Actions

The repository includes GitHub Actions to automatically deploy changes to AWS.

### Trigger Conditions

* Push to `main`
* Manual workflow dispatch (optional)

### What the Pipeline Does

1. Checkout repository
2. Build Docker image
3. Authenticate with AWS
4. Push image to **Amazon ECR**
5. Deploy updated image to AWS service (ECS / EC2 / etc.)

---

## 🔑 Required GitHub Secrets

These must be configured in the repository settings:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_REGION
AWS_ECR_REPOSITORY
```

Optional (depending on setup):

```text
ECS_CLUSTER
ECS_SERVICE
```

---

## ☁️ Deployment Flow

```text
Git Push → GitHub Actions → Docker Build → AWS ECR → AWS Runtime
```

Every merge to `main` results in a fresh deployment with the latest workflows.

---

## 🧪 Best Practices

* Export workflows **after every meaningful change**
* Keep workflows **stateless where possible**
* Use environment variables instead of hardcoded values
* Test locally before pushing to `main`

---

## 🛠️ Future Improvements

* Workflow versioning
* Environment-specific deployments (dev / staging / prod)
* Automated workflow validation
* Secrets injection via AWS Parameter Store

---

## 🤝 Contributing

1. Create a feature branch
2. Add or update workflows
3. Test locally
4. Open a pull request to `main`

---

## 📄 License

Internal use only — Eximso proprietary.

