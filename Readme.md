# Keystone Deployment

Kubernetes-based deployment infrastructure for the Keystone service using Kustomize for environment-specific configurations and GitHub Actions for automated CI/CD.

## 🚀 Overview

This repository contains the deployment configuration for the Keystone service, featuring:
- **Multi-environment support** (dev, stage, prod)
- **GitOps workflow** with automated deployments
- **Kubernetes native** using Kustomize for configuration management
- **AWS EKS** integration with ALB Ingress
- **Redis** for caching and session management
- **AWS Secrets Manager** integration for secure secret handling
- **Slack notifications** for deployment status updates

## 📁 Project Structure

```
.
├── .github/
│   └── workflows/
│       └── cd-dev.yaml              # Development deployment workflow
├── k8s/
│   ├── base/                        # Base Kubernetes manifests
│   │   ├── configmap/               # Configuration maps
│   │   ├── deployment/
│   │   │   ├── base-deploy.yaml     # Main application deployment
│   │   │   └── redis-deploy.yaml    # Redis deployment
│   │   ├── ingress/
│   │   │   └── base-ingress.yaml    # ALB Ingress configuration
│   │   ├── secretproviderclass/
│   │   │   └── base-secretproviderclass.yaml  # AWS Secrets Manager integration
│   │   ├── service/
│   │   │   ├── base-service.yaml    # Main service
│   │   │   └── redis-service.yaml   # Redis service
│   │   └── kustomization.yaml       # Base kustomization config
│   └── overlays/                    # Environment-specific configurations
│       ├── dev/
│       │   └── kustomization.yaml   # Development environment patches
│       ├── stage/
│       │   └── kustomization.yaml   # Staging environment patches
│       └── prod/
│           └── kustomization.yaml   # Production environment patches
└── README.md
```

## 🛠 Technology Stack

- **Kubernetes**: Container orchestration platform
- **Kustomize**: Configuration management for Kubernetes
- **GitHub Actions**: CI/CD pipeline automation
- **AWS EKS**: Managed Kubernetes service
- **AWS ALB**: Application Load Balancer for ingress
- **AWS Secrets Manager**: Secure secret storage
- **Redis**: In-memory data store for caching
- **Docker**: Container runtime

## 🚀 Deployment Workflow

### Automated Deployment (GitOps)

The deployment process is fully automated using GitHub Actions:

1. **Trigger**: Manual workflow dispatch with required parameters:
   - Docker image URL
   - Git commit SHA
   - Commit message

2. **Process**:
   - Updates the Kustomize configuration with new image tag
   - Commits changes to the repository
   - Configures AWS credentials
   - Updates EKS kubeconfig
   - Applies Kubernetes manifests using Kustomize
   - Monitors rollout status
   - Sends Slack notification with deployment status

### Manual Deployment

For manual deployments or testing:

```bash
# Apply to development environment
kubectl apply -k k8s/overlays/dev

# Apply to staging environment
kubectl apply -k k8s/overlays/stage

# Apply to production environment
kubectl apply -k k8s/overlays/prod
```

## 🌍 Environment Configuration

### Development Environment
- **Namespace**: `dev-keystone-service`
- **Domain**: `greatify.us`
- **Resources**: 
  - CPU: 400m requests, 500m limits
  - Memory: 600Mi requests, 700Mi limits
- **Replicas**: 1

### Staging Environment
- **Namespace**: `stage-keystone-service`
- **Domain**: staging-specific configuration
- **Resources**: Production-like resource allocation

### Production Environment
- **Namespace**: `prod-keystone-service`
- **Domain**: Production domain configuration
- **Resources**: Optimized for production workloads

## 🔐 Secret Management

Secrets are managed through AWS Secrets Manager and mounted into pods using the AWS Secrets Store CSI Driver:

- **Development**: `arn:aws:secretsmanager:ap-south-1:399600302704:secret:keystone-secrets-dev-Cs3Vge`
- Environment variables are loaded from `.env` file mounted as volume

## 📊 Monitoring and Observability

- **Deployment Status**: Automated rollout status checking
- **Slack Notifications**: Real-time deployment status updates
- **Kubernetes Annotations**: Change cause tracking for deployments
- **Resource Monitoring**: CPU and memory limits configured

## 🔧 Prerequisites

Before deploying, ensure you have:

1. **AWS Access**: 
   - Valid AWS credentials with EKS access
   - Configured AWS CLI

2. **Kubernetes Tools**:
   - `kubectl` installed and configured
   - `kustomize` (optional, `kubectl` has built-in support)

3. **GitHub Secrets** (for automated deployment):
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `AWS_REGION`
   - `DEV_CLUSTER_NAME`
   - `PAT_TOKEN`
   - `SLACK_WEBHOOK_URL`

## 🚦 Getting Started

1. **Clone the repository**:
   ```bash
   git clone <repository-url>
   cd keystone-deployment
   ```

2. **Configure AWS credentials**:
   ```bash
   aws configure
   ```

3. **Update kubeconfig**:
   ```bash
   aws eks update-kubeconfig --name <cluster-name> --region <region>
   ```

4. **Deploy to development**:
   ```bash
   kubectl apply -k k8s/overlays/dev
   ```

5. **Verify deployment**:
   ```bash
   kubectl get pods -n dev-keystone-service
   kubectl rollout status deployment/keystone-service -n dev-keystone-service
   ```

## 🔄 Update Deployment

To update the application with a new image:

1. **Automated** (Recommended): Use GitHub Actions workflow dispatch
2. **Manual**: Update the image tag in the appropriate overlay kustomization file

## 📝 Notes

- All deployments follow GitOps principles
- Configuration changes are version controlled
- Rollback is possible through git history
- Each environment has isolated configurations
- TLS certificates are managed through AWS Certificate Manager

## 🤝 Contributing

When making changes to the deployment configuration:

1. Test changes in development environment first
2. Follow the established Kustomize structure
3. Update this README if adding new features
4. Ensure proper resource limits are set
5. Test rollback procedures

## 📞 Support

For deployment issues or questions, contact the DevOps team or check the Slack notifications for real-time deployment status.
