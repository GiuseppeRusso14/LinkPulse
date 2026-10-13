# LinkPulse

Piattaforma di URL shortening con analytics dei click, progettata come applicazione **3-tier a microservizi** e distribuita prima su un cluster **Kubernetes locale** (Multipass) e poi su **AWS** con servizi gestiti.

Progetto per l'esame di **Sistemi Cloud** — Corso di Laurea Magistrale in Informatica (LM-18), Università degli Studi di Catania.

## Architettura in breve

| Tier | Componenti |
|---|---|
| Presentazione | `frontend` — React + Vite, servito da nginx |
| Applicativo | `auth-service`, `links-service`, `redirect-service`, `analytics-service` — Python Flask |
| Dati | PostgreSQL (dati principali), Redis (cache dei redirect), RabbitMQ (eventi di click) |

| | Versione locale | Versione AWS |
|---|---|---|
| Orchestrazione | Kubernetes (kubeadm) su VM Multipass | Amazon EKS |
| Database | PostgreSQL in cluster | Amazon RDS for PostgreSQL |
| Cache | Redis in cluster | Amazon ElastiCache for Redis |
| Coda messaggi | RabbitMQ in cluster | Amazon MQ for RabbitMQ |
| Registry immagini | GitHub Container Registry | Amazon ECR |
| Monitoraggio | — | CloudWatch + SNS |
| IaC | Terraform + Ansible | Terraform + Ansible |
| CI/CD | GitHub Actions | GitHub Actions |

## Struttura del repository

```
services/        microservizi Flask (auth, links, redirect, analytics)
frontend/        interfaccia React
k8s/             manifest Kubernetes (Kustomize: base + overlay local/aws)
infra/local/     Terraform e Ansible per il cluster Multipass
infra/aws/       Terraform e Ansible per l'infrastruttura AWS
.github/         workflow GitHub Actions
docs/            presentazione e relazione
```

## Documentazione

- Presentazione del progetto: [docs/presentazione/LinkPulse-Presentazione.pdf](docs/presentazione/LinkPulse-Presentazione.pdf)

## Autore

**Giuseppe Russo** — A.A. 2026/2027
