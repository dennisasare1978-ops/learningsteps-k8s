# LearningSteps Advanced - CI/CD Pipeline for Azure Kubernetes

Production-grade DevOps demonstration project showcasing automated deployment of a FastAPI application to Azure Kubernetes Service (AKS) with comprehensive security scanning and monitoring.

## Project Overview

This project demonstrates a complete end-to-end CI/CD pipeline for deploying containerized applications to Kubernetes on Azure. It includes infrastructure provisioning with Terraform, automated GitHub Actions workflows, security vulnerability scanning, and Kubernetes deployment best practices.

**Key Features**:
- 🚀 Automated CI/CD pipeline with GitHub Actions
- 🔒 Security-first approach with Trivy vulnerability scanning
- ☸️ Kubernetes deployment with namespace isolation and RBAC
- 🏗️ Infrastructure as Code with Terraform
- 📊 Prometheus metrics and health checks
- 🐳 Docker containerization with non-root execution
- 🔐 Secret management with Kubernetes secrets

## Quick Start

### Prerequisites

- Azure subscription with Contributor access
- Azure CLI installed and configured (`az login`)
- kubectl installed locally
- Terraform installed (for infrastructure provisioning)
- GitHub repository with Actions enabled

### High-Level Steps

1. **Provision Infrastructure**: Use Terraform to create AKS cluster and PostgreSQL database
2. **Configure Kubernetes**: Set up namespace, RBAC, and service accounts
3. **Configure GitHub Secrets**: Add ACR and Kubernetes credentials
4. **Deploy Application**: Push to main branch triggers automated deployment

## Documentation

📖 **[Application Deployment Guide](app/README.md)** - Complete setup instructions, troubleshooting, and operational procedures

🏗️ **[Infrastructure Setup Guide](infra-terraform/README.md)** - Terraform configuration and Azure resource provisioning

## Technology Stack

**Application**:
- FastAPI (Python 3.11)
- PostgreSQL with asyncpg
- Prometheus metrics
- Uvicorn ASGI server

**Infrastructure**:
- Azure Kubernetes Service (AKS)
- Azure Container Registry (ACR)
- Azure PostgreSQL Flexible Server
- Terraform for IaC

**CI/CD**:
- GitHub Actions
- Trivy security scanner
- Docker

**Kubernetes**:
- Namespace isolation
- RBAC with ServiceAccount
- LoadBalancer service
- Rolling deployments
- Health checks

## Pipeline Overview

The CI/CD pipeline consists of 5 automated stages:

1. **Code Security Scan** - Trivy scans source code for vulnerabilities
2. **Build, Scan & Push** - Build Docker image, scan for CRITICAL CVEs (blocks deployment if found), push to ACR
3. **Configure Kubernetes** - Set up kubectl with service account authentication
4. **Deploy to AKS** - Update deployment with new image tag, apply manifests
5. **Verify Deployment** - Wait for rollout, check pod status, verify service

**Triggers**:
- Push to `main`/`master` (full deployment)
- Pull requests (build and scan only)
- Manual workflow dispatch

## Project Structure

```
learningsteps-advanced/
├── app/                           # FastAPI application
│   ├── main.py                    # API endpoints and metrics
│   ├── repositories/              # Database layer with auto-init
│   ├── Dockerfile                 # Container image definition
│   ├── requirements.txt           # Python dependencies
│   └── README.md                  # 📖 Deployment guide
│
├── infra-terraform/               # Infrastructure as Code
│   ├── main.tf                    # Resource definitions
│   ├── variables.tf               # Input variables
│   ├── outputs.tf                 # Output values
│   ├── provider.tf                # Azure provider config
│   ├── terraform.tfvars           # Variable values
│   └── README.md                  # 🏗️ Infrastructure guide
│
├── k8s/                           # Kubernetes manifests
│   ├── namespace.yaml             # learningsteps namespace
│   ├── github-deployer-sa.yaml    # ServiceAccount + RBAC
│   ├── github-deployer-secret.yaml # Token secret
│   ├── deployment.yaml            # Application deployment
│   └── service.yaml               # LoadBalancer service
│
├── .github/workflows/             # CI/CD pipeline
│   └── app-pipeline.yml           # GitHub Actions workflow
│
├── CONTEXT.md                     # 📋 Comprehensive technical docs
├── CLAUDE.md                      # Instructions for Claude Code
└── README.md                      # This file
```

## Security Features

- **Vulnerability Scanning**: Trivy blocks deployment if CRITICAL vulnerabilities found
- **Non-Root Containers**: Application runs as UID 1000 (appuser)
- **Namespace Isolation**: Dedicated namespace with scoped RBAC
- **Service Account Auth**: GitHub Actions uses service account with minimal permissions
- **Secret Management**: Database credentials in Kubernetes secrets
- **Token-Based ACR Auth**: Uses scoped tokens instead of admin credentials
- **Security Context**: Dropped capabilities and read-only filesystem

## Monitoring & Observability

- **Health Checks**: `/health` endpoint for liveness and readiness probes
- **Prometheus Metrics**: `/metrics` endpoint with request/latency metrics
- **Swagger UI**: `/docs` endpoint for interactive API documentation
- **Application Logs**: Structured logging for troubleshooting


## Troubleshooting

### Pipeline Fails at Image Scan
- Update base image to newer version
- Update Python dependencies in requirements.txt
- Run `apt-get upgrade` in Dockerfile

### Pods Not Starting
- Check logs: `kubectl logs -l app=learningsteps-api -n learningsteps`
- Check events: `kubectl describe pod <POD> -n learningsteps`
- Verify database secret exists: `kubectl get secret learningsteps-secrets -n learningsteps`

### LoadBalancer External IP Pending
- Wait 2-5 minutes for Azure to provision
- Check service: `kubectl describe svc learningsteps-api -n learningsteps`

For detailed troubleshooting, see [app/README.md](app/README.md).

## Cleanup

```bash
# Delete Kubernetes resources
kubectl delete namespace learningsteps

# Delete ACR images and token
az acr repository delete --name acrwesteu --repository learningsteps-api --yes
az acr token delete --name github-actions-token --registry acrwesteu --yes

# Destroy infrastructure
cd infra-terraform
terraform destroy
```

## Learning Resources

This project demonstrates key DevOps concepts:

- **CI/CD Pipelines**: Multi-stage automated deployments with quality gates
- **Container Security**: Vulnerability scanning, non-root execution, secret management
- **Kubernetes Best Practices**: RBAC, namespaces, health checks, rolling updates
- **Infrastructure as Code**: Reproducible infrastructure with Terraform
- **Cloud-Native Architecture**: 12-factor app principles, stateless design, observability

## Contributing

This is a learning/demonstration project. Feel free to:
- Fork and experiment
- Adapt for your own use cases
- Suggest improvements via issues
- Share feedback on the architecture

## License

[Add your license here]

## Support

- **Issues**: Report bugs or request features via GitHub Issues
- **Documentation**: See [app/README.md](app/README.md) and [CONTEXT.md](CONTEXT.md)
- **Infrastructure**: See [infra-terraform/README.md](infra-terraform/README.md)

---

**Note**: This project is designed for learning and demonstration purposes. For production use, consider additional security measures like TLS/HTTPS, Ingress controllers, network policies, private AKS clusters, and integration with Azure Key Vault. See [CONTEXT.md](CONTEXT.md) for production recommendations.
# trigger pipeline
# trigger pipeline
