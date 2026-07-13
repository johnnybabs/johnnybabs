# John Babalola

Cloud DevOps Engineer in Belfast, UK. I build and operate AWS infrastructure: Kubernetes on EKS, Terraform, GitHub Actions CI/CD with OIDC, and Prometheus/Grafana observability. Currently completing AWS Solutions Architect Associate (SAA-C03) and Microsoft Azure Fundamentals (AZ-900). Background in power systems engineering (MSc, University of Ibadan) and doctoral research in blockchain for off-grid renewable energy (University of Johannesburg, admitted).

Website: [johnbabalola.com](https://johnbabalola.com) | Email: baabalola@gmail.com

## Projects

| Project | What it is | Stack |
|---|---|---|
| [VidCast](https://github.com/johnnybabs/vidcast) | Python microservices video platform on AWS EKS: 5 Terraform modules, GitHub Actions with OIDC, Trivy/Gitleaks/Checkov security gates, kube-prometheus-stack with SLO and Kubecost dashboards, DLQ retry topology and idempotent consumers | EKS, Terraform, GitHub Actions, Jenkins, Kustomize, Prometheus, Grafana, Redis, RabbitMQ |
| [CBAPrints](https://github.com/johnnybabs/cbaprints) | Serverless e-commerce stack: 9 nested CloudFormation stacks, SQS with DLQ resilience, compute on Lambda and ECS Fargate | CloudFormation, Lambda, ECS Fargate, SQS, DynamoDB |
| [Student Portal HA](https://github.com/johnnybabs/student-portal-ha-aws) | Two-tier high availability web architecture: VPC across 2 AZs, RDS Multi-AZ failover, 5 chained security groups, SSM Parameter Store secrets | VPC, EC2, RDS Multi-AZ, ALB, SSM |

All three are solo builds from the Cloud Booster Academy UK cloud engineering programme (February to June 2026), each with infrastructure as code, CI/CD and documented teardown.

## Currently building

A portfolio site on S3 + CloudFront + Route 53 deployed by GitHub Actions, then a GitOps upgrade of VidCast (Helm, ArgoCD, OpenTelemetry) and an AIOps assistant on Amazon Bedrock. Write-ups land on [johnbabalola.com](https://johnbabalola.com) and Medium as they ship.
