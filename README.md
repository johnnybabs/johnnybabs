# 👋 Hi, I'm John Babalola

**Cloud DevOps Engineer**
AWS · Kubernetes · Terraform · GitHub Actions · Jenkins · Docker · Prometheus · Python
Belfast, Northern Ireland, United Kingdom

📧 <baabalola@gmail.com>

[![GitHub](https://img.shields.io/badge/GitHub-000?style=for-the-badge&logo=github&logoColor=white)](https://github.com/johnnybabs) [![Portfolio](https://img.shields.io/badge/Portfolio-4A90E2?style=for-the-badge&logo=amazons3&logoColor=white)](https://johnbabalola.com)

---

## 🚀 About Me

Cloud DevOps engineer who builds and operates AWS infrastructure end to end: Terraform-provisioned EKS platforms, CI/CD with GitHub Actions OIDC and Jenkins, security-gated pipelines, and Prometheus/Grafana observability with SLO and cost dashboards. Background in power systems engineering (MSc), now applying the same systems discipline to cloud infrastructure.

Recent highlights:

- 🎬 Built **VidCast**, a Python microservices video platform on **AWS EKS**: 5 Terraform modules, GitHub Actions with OIDC and a matrix build across 4 services, Trivy/Gitleaks/Checkov security gates, kube-prometheus-stack with SLO/error budget and Kubecost FinOps dashboards, DLQ retry topology and Redis-based idempotent consumers
- 🛒 Built a **serverless e-commerce order pipeline** across **9 CloudFormation nested stacks**: API Gateway, Lambda, SQS with DLQ resilience, ECS Fargate, SES, DynamoDB
- 🏫 Built a **two-tier high availability student portal**: VPC across 2 AZs, RDS Multi-AZ failover, 5 chained security groups (identity-based, no CIDR trust), SSM Parameter Store secrets
- 🔒 Every project ships with infrastructure as code, a security-scanned pipeline, documented teardown and a cost table

Currently pursuing **AWS Solutions Architect Associate (SAA-C03)** and **Microsoft Azure Fundamentals (AZ-900)**. Open to Cloud Engineer, DevOps Engineer, SRE and Platform Engineer roles in the UK.

---

## 🛠️ Tech Stack

### ☁️ Cloud & Infrastructure

![AWS](https://img.shields.io/badge/AWS-FF9900?logo=amazonwebservices&logoColor=white) ![Azure](https://img.shields.io/badge/Azure-0078D4?logo=microsoftazure&logoColor=white) ![Terraform](https://img.shields.io/badge/Terraform-7B42BC?logo=terraform&logoColor=white) ![CloudFormation](https://img.shields.io/badge/CloudFormation-FF4F00?logo=amazonwebservices&logoColor=white) ![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?logo=kubernetes&logoColor=white) ![EKS](https://img.shields.io/badge/Amazon%20EKS-FF9900?logo=amazoneks&logoColor=white)

### ⚙️ CI/CD & DevOps

![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?logo=githubactions&logoColor=white) ![Jenkins](https://img.shields.io/badge/Jenkins-D24939?logo=jenkins&logoColor=white) ![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white) ![Kustomize](https://img.shields.io/badge/Kustomize-326CE5?logo=kubernetes&logoColor=white) ![Bash](https://img.shields.io/badge/Bash-4EAA25?logo=gnubash&logoColor=white)

### 📈 Observability & FinOps

![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?logo=prometheus&logoColor=white) ![Grafana](https://img.shields.io/badge/Grafana-F46800?logo=grafana&logoColor=white) ![Alertmanager](https://img.shields.io/badge/Alertmanager-E6522C?logo=prometheus&logoColor=white) ![Kubecost](https://img.shields.io/badge/Kubecost-563D7C?logo=kubernetes&logoColor=white)

### 🔐 Security Tooling

![Trivy](https://img.shields.io/badge/Trivy-1904DA?logo=trivy&logoColor=white) ![Gitleaks](https://img.shields.io/badge/Gitleaks-2088FF?logo=git&logoColor=white) ![Checkov](https://img.shields.io/badge/Checkov-9F2B68?logo=prisma&logoColor=white)

### 🧰 Languages & Data

![Python](https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white) ![SQL](https://img.shields.io/badge/SQL-4479A1?logo=postgresql&logoColor=white) ![Flask](https://img.shields.io/badge/Flask-000000?logo=flask&logoColor=white) ![Redis](https://img.shields.io/badge/Redis-DC382D?logo=redis&logoColor=white) ![RabbitMQ](https://img.shields.io/badge/RabbitMQ-FF6600?logo=rabbitmq&logoColor=white)

---

## 🏗️ Featured Projects

### 🎬 [VidCast](https://github.com/johnnybabs/vidcast) — Microservices Video Platform on EKS

Python microservices (Flask, Pika, MoviePy) deployed on AWS EKS in eu-west-2 with Kustomize overlays for dev and prod. Reliability engineered in: transactional outbox pattern, DLQ retry topology with a dead-letter exchange, and idempotent consumers via Redis SET NX EX. Pod security hardened: non-root, read-only root filesystem, dropped capabilities, resource limits on every container.

Provisioned by **5 Terraform modules** (vpc, iam, eks, security-groups, github-oidc) with S3 remote state and DynamoDB locking. **GitHub Actions CI/CD with OIDC** (no long-lived AWS keys), matrix build across 4 services, Trivy CRITICAL/HIGH merge gate, Gitleaks and Checkov scanning, plus a Jenkins staging pipeline with a manual approval gate. Observability via kube-prometheus-stack with SLO/error budget and Kubecost FinOps dashboards.

`Python` `Flask` `EKS` `Terraform` `GitHub Actions` `OIDC` `Jenkins` `Kustomize` `Prometheus` `Grafana` `Kubecost` `Trivy` `Gitleaks` `Checkov` `Redis` `RabbitMQ`

---

### 🛒 [CBAPrints](https://github.com/johnnybabs/cbaprints) — Serverless E-Commerce Pipeline on AWS

Event-driven order processing across **9 CloudFormation nested stacks**: API Gateway, Lambda, SQS with dead-letter queue resilience, ECS Fargate, S3 and SES, with DynamoDB order tracking. Built as a production-style serverless stack with alerting and least-privilege IAM throughout.

`CloudFormation` `Lambda` `ECS Fargate` `SQS` `SES` `DynamoDB` `Python` `IAM`

---

### 🏫 [Student Portal HA](https://github.com/johnnybabs/student-portal-ha-aws) — Two-Tier High Availability Architecture

Classic two-tier web architecture built for failure: VPC across 2 Availability Zones, RDS Multi-AZ with tested failover, ALB-fronted web tier, and **5 chained security groups** where every rule trusts another security group rather than a CIDR range. Secrets held in SSM Parameter Store, nothing hardcoded.

`VPC` `EC2` `ALB` `RDS Multi-AZ` `Security Groups` `SSM Parameter Store` `Terraform`

---

### ☁️ Azure Case Study Labs

Two Azure labs: a serverless order processing flow and a storage monitoring solution, built alongside AZ-900 preparation.

`Azure` `Serverless` `Azure Monitor`

---

## 🔭 Currently Building

A public build-in-the-open roadmap through to December 2026: this portfolio site on S3 + CloudFront + Route 53 deployed by GitHub Actions OIDC, a GitOps upgrade of VidCast (Helm, ArgoCD, OpenTelemetry, a live EKS version upgrade), Ansible configuration management, an AIOps incident assistant on Amazon Bedrock, and a production-ops evidence pack (AWS FIS chaos drills, DR restores, blameless postmortems). Each ships with a case study on [johnbabalola.com](https://johnbabalola.com) and a Medium write-up.

---

## 🎓 Certifications & Education

| Qualification | Status |
| --- | --- |
| AWS Solutions Architect Associate (SAA-C03) | 🔄 In progress, 2026 |
| Microsoft Azure Fundamentals (AZ-900) | 🔄 In progress, 2026 |
| Cloud Booster Academy UK, Cloud Engineering Programme (AWS, Terraform, Kubernetes, CI/CD) | ✅ Feb to Jun 2026 |
| PhD Candidate, University of Johannesburg (blockchain and TinyFL for off-grid renewable energy) | 🔄 Admitted, in progress |
| MSc Power Systems Engineering, University of Ibadan | ✅ |
| BSc Electrical and Electronic Engineering, University of Ibadan | ✅ |

---

## 🤝 Open To

Cloud Engineer · DevOps Engineer · Site Reliability Engineer · Platform Engineer

Belfast or remote UK 

📧 **<baabalola@gmail.com>** | 🌐 **[johnbabalola.com](https://johnbabalola.com)**

**Infrastructure that is provisioned as code, shipped through a gated pipeline, observed in production and torn down cleanly.**
