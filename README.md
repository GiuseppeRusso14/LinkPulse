<div align="center">

# LinkPulse

**URL shortener con statistiche dei click, realizzato a microservizi su Kubernetes e AWS**

Progetto d'esame di **Sistemi Cloud e Laboratorio**
Corso di Laurea Magistrale in Informatica · Università degli Studi di Catania · A.A. 2026/2027

![Python](https://img.shields.io/badge/Python-Flask-3776AB?logo=python&logoColor=white)
![React](https://img.shields.io/badge/React-Vite-61DAFB?logo=react&logoColor=black)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?logo=redis&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/RabbitMQ-FF6600?logo=rabbitmq&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?logo=kubernetes&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?logo=amazonwebservices&logoColor=white)
![Terraform](https://img.shields.io/badge/Terraform-7B42BC?logo=terraform&logoColor=white)
![Ansible](https://img.shields.io/badge/Ansible-EE0000?logo=ansible&logoColor=white)

</div>

---

## Il progetto

LinkPulse è un servizio che accorcia gli indirizzi web e tiene traccia di quante volte, e da dove, vengono aperti.
Un utente registrato può:

- creare link brevi, anche con un nome scelto da lui e una data di scadenza;
- condividerli: chi apre il link viene reindirizzato all'indirizzo originale;
- consultare per ogni link il numero di click nel tempo, i dispositivi, i browser e la provenienza.

L'obiettivo, in linea con il programma del corso, è realizzare l'applicazione come insieme di **microservizi in container**, distribuirla prima su un **cluster Kubernetes locale** e poi su **AWS con servizi gestiti**, automatizzando infrastruttura e rilasci.

## Architettura

Applicazione **3-tier** a microservizi, con comunicazione asincrona per la registrazione dei click.

```mermaid
flowchart LR
    U([Browser]) --> I[Ingress nginx]

    subgraph K8s [Cluster Kubernetes]
        I --> F[frontend<br/>React]
        I --> A[auth-service]
        I --> L[links-service]
        I --> R[redirect-service]
        I --> AN[analytics-service]
        R -- eventi di click --> Q[(RabbitMQ)]
        Q --> AN
        R <--> C[(Redis)]
        L --> C
    end

    A --> DB[(PostgreSQL)]
    L --> DB
    AN --> DB
```

| Componente | Ruolo |
|---|---|
| `frontend` | Interfaccia web: login, gestione dei link, dashboard con i grafici |
| `auth-service` | Registrazione e login degli utenti, rilascio del token JWT |
| `links-service` | Creazione, modifica ed eliminazione dei link |
| `redirect-service` | Risolve il link breve, reindirizza all'indirizzo originale e segnala il click |
| `analytics-service` | Elabora i click ricevuti dalla coda e calcola le statistiche |
| PostgreSQL | Database principale: utenti, link e click, con uno schema per servizio |
| Redis | Cache degli indirizzi più richiesti, per non interrogare il database a ogni click |
| RabbitMQ | Coda degli eventi di click tra `redirect-service` e `analytics-service` |

Il redirect non attende il salvataggio del click: l'evento resta in coda finché `analytics-service` non lo elabora, così la latenza percepita dall'utente resta bassa anche sotto carico.

## Dal cluster locale ad AWS

| | Versione locale | Versione AWS |
|---|---|---|
| Orchestrazione | Kubernetes (kubeadm) su VM Multipass | Amazon EKS |
| Database | PostgreSQL nel cluster | Amazon RDS for PostgreSQL |
| Cache | Redis nel cluster | Amazon ElastiCache for Redis |
| Coda di messaggi | RabbitMQ nel cluster | Amazon MQ for RabbitMQ |
| Registry delle immagini | GitHub Container Registry | Amazon ECR |
| Monitoraggio | — | Amazon CloudWatch + SNS |
| Infrastructure as Code | Terraform + Ansible | Terraform + Ansible |
| CI/CD | GitHub Actions | GitHub Actions |

## Argomenti del corso affrontati

- **Containerizzazione** dei servizi con Docker
- **Orchestrazione** con Kubernetes: deployment, service, ingress, scalabilità orizzontale
- **Cluster reale in locale** con macchine virtuali Multipass
- **Infrastructure as Code** con Terraform (provisioning) e Ansible (configurazione)
- **Migrazione su cloud pubblico** con servizi gestiti AWS
- **Alta disponibilità** su più Availability Zone e Auto Scaling
- **Comunicazione asincrona** tra microservizi tramite code di messaggi
- **Monitoraggio e notifiche** con CloudWatch e SNS
- **CI/CD** con GitHub Actions

## Stato del progetto

| Attività | Stato |
|---|---|
| Progettazione e presentazione del progetto | ✅ Completata |
| Microservizi e frontend | 🔄 In corso |
| Deployment su Kubernetes locale | ⏳ Da fare |
| Deployment su AWS | ⏳ Da fare |
| Pipeline CI/CD | ⏳ Da fare |

Il repository viene aggiornato man mano che il lavoro procede.

## Documentazione

- 📄 [Presentazione del progetto (PDF)](docs/presentazione/LinkPulse-Presentazione.pdf)

## Autore

**Giuseppe Russo** · Università degli Studi di Catania
