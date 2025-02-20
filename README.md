# DevOps Automation Suite

A comprehensive portfolio project demonstrating DevOps best practices including Infrastructure as Code, CI/CD pipelines, and GitOps workflows.

![DevOps Workflow](./docs/project-architecture.md)

## Features

- **Infrastructure as Code** with Terraform
  - AWS cloud resources provisioning
  - Modularized infrastructure components
  - State management with remote backend

- **Containerized Applications**
  - Kubernetes deployments 
  - Resource management
  - Horizontal Pod Autoscaling

- **GitOps Workflow**
  - ArgoCD for declarative deployments
  - Git as the single source of truth
  - Automated syncing and drift detection

- **CI/CD Pipeline**
  - GitHub Actions for CI/CD
  - Automated testing and deployment
  - Infrastructure validation

- **Monitoring & Observability**
  - Prometheus for metrics collection
  - Grafana for visualization
  - Alerting configuration

## Project Structure

```
devops-automation-suite/
├── .github/                 # CI/CD pipeline configurations
│   └── workflows/           # GitHub Actions workflows
├── terraform/               # Infrastructure as Code
│   ├── modules/             # Reusable Terraform modules
│   └── environments/        # Environment-specific configurations
├── kubernetes/              # Kubernetes manifests
│   ├── manifests/           # Application manifests
│   └── helm-charts/         # Helm charts for third-party tools
├── argocd/                  # ArgoCD configurations
│   ├── applications/        # Application definitions
│   └── projects/            # Project configurations
└── docs/                    # Documentation
```

## Getting Started

### Prerequisites

- AWS Account
- GitHub Account
- kubectl
- Terraform
- AWS CLI
- ArgoCD CLI (optional)

### Setup Instructions

1. **Clone the repository**
   ```bash
   git clone https://github.com/GooeyTuxedo/devops-automation-suite.git
   cd devops-automation-suite
   ```

2. **Set up AWS credentials**
   ```bash
   aws configure
   ```

3. **Create Terraform backend resources**
   ```bash
   # Create S3 bucket for state
   aws s3 mb s3://devops-automation-suite-terraform-state

   # Enable versioning
   aws s3api put-bucket-versioning \
     --bucket devops-automation-suite-terraform-state \
     --versioning-configuration Status=Enabled

   # Create DynamoDB table for locking
   aws dynamodb create-table \
     --table-name devops-automation-suite-terraform-locks \
     --attribute-definitions AttributeName=LockID,AttributeType=S \
     --key-schema AttributeName=LockID,KeyType=HASH \
     --billing-mode PAY_PER_REQUEST
   ```

4. **Deploy infrastructure**
   ```bash
   cd terraform/environments/dev
   terraform init
   terraform plan
   terraform apply
   ```

5. **Configure kubectl**
   ```bash
   aws eks update-kubeconfig --region us-east-1 --name devops-automation-suite-dev-cluster
   ```

6. **Install ArgoCD**
   ```bash
   kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

7. **Deploy ArgoCD configurations**
   ```bash
   kubectl apply -f argocd/projects/devops-automation-suite.yaml
   kubectl apply -f argocd/applications/
   ```

## Infrastructure Components

### AWS Resources

- **VPC and Networking**
  - Public and private subnets
  - NAT Gateways
  - Security Groups

- **EKS Cluster**
  - Managed Kubernetes control plane
  - Auto-scaling node groups
  - IAM integration

- **S3 Buckets**
  - Application assets
  - Terraform state

### Kubernetes Resources

- **Application Deployment**
  - Deployments and Services
  - Ingress controllers
  - Horizontal Pod Autoscaler

- **Monitoring Stack**
  - Prometheus for metrics
  - Grafana for dashboards
  - Alertmanager for alerts

## CI/CD Pipeline

The CI/CD pipeline uses GitHub Actions and consists of the following stages:

1. **Validate Infrastructure**
   - Terraform format check
   - Terraform validation

2. **Build Application**
   - Build Docker images
   - Push to ECR

3. **Deploy Infrastructure**
   - Apply Terraform changes
   - Update EKS configuration

4. **Deploy Application**
   - Update Kubernetes manifests
   - Verify deployment

## GitOps with ArgoCD

This project uses ArgoCD for GitOps-based deployments:

- Applications defined as YAML manifests
- Automatic synchronization
- Drift detection and remediation
- Application of Kubernetes resources

## Monitoring and Observability

The monitoring stack includes:

- **Prometheus** for metrics collection
- **Grafana** for visualization with pre-configured dashboards
- **Alertmanager** for alert routing

## Security Considerations

- **Network Security**
  - Private subnets for workloads
  - Security groups with least privilege
  - Network policies

- **Identity and Access**
  - IAM roles with minimal permissions
  - RBAC for Kubernetes resources
  - Service accounts

- **Data Protection**
  - Encrypted S3 buckets
  - Secrets management
  - TLS for ingress

## Extending the Project

Here are some ways to extend this devops automation project:

1. **Multi-environment Setup**
   - Add production environment
   - Implement promotion workflows

2. **Additional AWS Services**
   - RDS for databases
   - ElastiCache for caching
   - CloudFront for CDN

3. **Advanced Monitoring**
   - Distributed tracing with Jaeger
   - Log aggregation with ELK stack

## License

MIT

## Acknowledgments

- AWS Documentation
- Terraform Registry
- Kubernetes Documentation
- ArgoCD Documentation