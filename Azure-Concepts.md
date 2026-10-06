## AZURE CLOUD

```
                            ┌─────────────────────────────┐
                            │      AZURE CLOUD FOR        │
                            │    JAVA BACKEND DEVELOPER   │
                            └─────────────┬───────────────┘
                                          │
        ┌─────────────┬──────────┬────────┼────────┬──────────────┬──────────────┐
        │             │          │        │        │              │              │
        ▼             ▼          ▼        ▼        ▼              ▼              ▼
   FUNDAMENTALS   COMPUTE    STORAGE    DATA   MESSAGING    SECURITY      DEVOPS &
                                                                         MONITORING
```

### Azure Fundamentals

| Topic | Key Points |
|-------|-----------|
| **Cloud model** | Microsoft manages infra, pay-as-you-go, shared responsibility model |
| **IaaS** | Virtual Machines — you manage OS + app |
| **PaaS** | App Service — you manage app only (most common for Java devs) |
| **SaaS** | Microsoft 365 — just use it |
| **Resource Group** | Logical container for resources. Apply RBAC, cost tracking at group level. Deploy/delete together. Example: rg-dev, rg-staging, rg-prod |

### Azure Compute

| Topic | Key Points |
|-------|-----------|
| **App Service** | PaaS, deploy JAR/WAR directly, supports Java 8/11/17/21, auto-scale, deployment slots for zero-downtime. Best for: single apps, simple REST APIs. Plugin: `azure-webapp-maven-plugin` |
| **Azure Functions** | Serverless, event-driven, pay-per-execution, auto-scale from 0. Triggers: HTTP, Timer, Blob, Queue, Event Hub, Cosmos DB. Plans: Consumption (cheapest), Premium (no cold start), Dedicated. Best for: short background tasks. Limit: 5-10 min default |
| **AKS** | Managed Kubernetes. Azure manages control plane, you manage worker nodes. Key concepts: Pod, Deployment, Service, Ingress, ConfigMap, Secret, HPA, Namespace. Workflow: Code → JAR → Docker → ACR → AKS (Helm). Best for: 10+ microservices |
| **When to use what** | Single app → App Service. Background tasks → Functions. Microservices → AKS |

### Azure Storage

| Topic | Key Points |
|-------|-----------|
| **Blob Storage** | Unstructured data (images, videos, backups). Hierarchy: Storage Account → Container → Blobs. SDK: `azure-storage-blob` |
| **Blob types** | Block Blob (files, most common), Append Blob (logs), Page Blob (VM disks) |
| **Access tiers** | Hot (frequent, expensive storage), Cool (monthly access), Archive (rare, cheapest). Use lifecycle policies to auto-move between tiers |

### Azure Databases

| Topic | Key Points |
|-------|-----------|
| **Azure SQL** | Managed SQL Server. Structured data, joins, ACID. Java: JDBC, Spring Data JPA, Hibernate. Best for: e-commerce, banking, CRUD |
| **Cosmos DB** | NoSQL, globally distributed, multi-model (SQL, MongoDB, Cassandra, Gremlin). Single-digit ms latency. Best for: IoT, real-time, global apps, flexible schema |
| **When to use what** | Structured + relationships + transactions → SQL. Flexible schema + global scale + low latency → Cosmos DB |

### Azure Messaging

| Topic | Key Points |
|-------|-----------|
| **Service Bus** | Enterprise message broker (like RabbitMQ). Queues (1:1) + Topics (1:many). Guaranteed ordering, delivery, deduplication. Dead-letter queue. Supports transactions. Java: `spring-cloud-azure-starter-servicebus-jms` + `@JmsListener`. Best for: order processing, payment workflows |
| **Event Hubs** | Event streaming (like Kafka). Millions/sec. Multiple consumers read same events. Partition-based, time-based retention (1-90 days). Kafka-compatible API. Java: `spring-cloud-azure-starter-eventhubs` or Spring Kafka. Best for: telemetry, logging, analytics |
| **When to use what** | Reliable 1-time processing → Service Bus. High-throughput streaming → Event Hubs |

### Azure Security

| Topic | Key Points |
|-------|-----------|
| **Azure AD / Entra ID** | Cloud identity & access management. Auth + AuthZ + SSO. Concepts: Tenant, App Registration, Service Principal, Managed Identity, OAuth 2.0, JWT. Flow: User → Login → Azure AD → JWT → App validates. Java: `spring-cloud-azure-starter-active-directory` |
| **Managed Identity** | Azure manages credentials automatically — NO secrets in code. More secure than Service Principal. Use for apps running on Azure (App Service, AKS) |
| **Key Vault** | Stores secrets, keys, certificates. No passwords in code/config. Audit logs, secret rotation. Java: `spring-cloud-azure-starter-keyvault-secrets` → `@Value("${secret-name}")` auto-maps. Best practice: Key Vault + Managed Identity = zero credentials |
| **VNet** | Private isolated network. Concepts: VNet, Subnet, NSG (firewall rules), Private Endpoint (private IP access to Azure services), VNet Peering, VPN Gateway. Architecture: Internet → App Gateway only → AKS in private subnet → DB via Private Endpoint |
| **Golden rule** | Never expose backend to internet. Use Private Endpoints + Managed Identity + VNet + NSG |

### Azure DevOps & Monitoring

| Topic | Key Points |
|-------|-----------|
| **Azure DevOps** | 5 services: Repos (Git), Pipelines (CI/CD), Boards (like Jira), Artifacts (Maven/npm), Test Plans. CI: checkout → Maven build → tests → SonarQube → Docker → push ACR. CD: Deploy DEV → integration tests → STAGING → approval gate → PROD |
| **Azure Monitor** | Centralized monitoring: Metrics, Logs, Traces |
| **Application Insights** | APM for Java apps. Auto-tracks: request rates, response times, failures, dependency calls, exceptions, distributed tracing. Java: `applicationinsights-spring-boot-starter` |
| **Log Analytics** | Central log store. Query with KQL. Example: `requests \| where success == false \| where timestamp > ago(24h)` |
| **Alerts** | Response time > 3s → email. Error rate > 5% → page on-call. Action Groups: email, SMS, Teams, webhook |
| **AKS monitoring** | Container Insights (node/pod health) + Prometheus/Grafana + Application Insights (app-level) |

### Spring Boot Azure Dependencies (Quick Reference)

| Service | Dependency |
|---------|-----------|
| Azure AD | `spring-cloud-azure-starter-active-directory` |
| Key Vault | `spring-cloud-azure-starter-keyvault-secrets` |
| Service Bus | `spring-cloud-azure-starter-servicebus-jms` |
| Event Hubs | `spring-cloud-azure-starter-eventhubs` |
| Blob Storage | `azure-storage-blob` |
| App Insights | `applicationinsights-spring-boot-starter` |
| App Service Deploy | `azure-webapp-maven-plugin` |

### Azure Interview Power Answers

| Question | Power Answer |
|----------|-------------|
| Where do you store secrets? | Key Vault + Managed Identity — zero credentials in code |
| How do you deploy? | CI/CD via Azure DevOps — Maven → Docker → ACR → AKS via Helm |
| How do you monitor? | Application Insights for APM, Log Analytics for logs, Alerts for proactive notifications |
| How do you secure APIs? | Azure AD with OAuth 2.0 — JWT token validation in Spring Boot |
| Why AKS over App Service? | 15+ microservices needing fine-grained scaling and network control |
| How do you handle messaging? | Service Bus for commands/workflows, Event Hubs for high-throughput streaming |
| How do you secure the network? | VNet + Private Endpoints + NSG — backend never exposed to internet |

### Azure Flashcards

### Card 27: Azure Service Models
- **Q:** IaaS vs PaaS vs SaaS with Azure examples?
- **A:** IaaS = VMs (you manage OS+app). PaaS = App Service (you manage app only). SaaS = Microsoft 365 (just use it). Java devs mostly use PaaS. Resource Group = logical container for resources, apply RBAC and cost tracking at group level.

### Card 28: Azure Compute — When to Use What
- **Q:** App Service vs Azure Functions vs AKS?
- **A:** App Service: single app, simple deployment, deploy JAR directly. Functions: serverless, event-driven, short background tasks (image compression, queue processing), pay-per-execution. AKS: 10+ microservices, fine-grained scaling, complex networking. Workflow: Code → JAR → Docker → ACR → AKS via Helm.

### Card 29: Azure Messaging — Service Bus vs Event Hubs
- **Q:** When Service Bus vs Event Hubs?
- **A:** Service Bus = message broker (like RabbitMQ). Queues + Topics, guaranteed ordering, dead-letter queue, transactions. Best for: order/payment workflows. Event Hubs = event streaming (like Kafka). Millions/sec, multiple consumers, partition-based. Best for: telemetry, logging, analytics. Can use both in same project.

### Card 30: Azure Security Stack
- **Q:** How to secure a Java backend on Azure end-to-end?
- **A:** Identity: Azure AD + OAuth 2.0 + JWT validation. Secrets: Key Vault + Managed Identity (zero credentials in code). Network: VNet + Private Endpoints + NSG (backend never exposed to internet). API: `@PreAuthorize` for role-based access. Golden rule: Managed Identity for service-to-service, Private Endpoints for all Azure services.

### Card 31: Azure Monitoring Stack
- **Q:** How to monitor Java microservices on AKS?
- **A:** Application Insights for APM (requests, dependencies, exceptions, distributed tracing). Log Analytics for centralized logs (query with KQL). Alerts for proactive notification (error rate > 5% → page on-call). Container Insights for AKS cluster health. Three pillars: Metrics + Logs + Traces. Java: `applicationinsights-spring-boot-starter`.

### Card 32: Azure DevOps CI/CD Pipeline
- **Q:** Describe a CI/CD pipeline for Java + AKS?
- **A:** CI: trigger on push → Maven build → unit tests → SonarQube scan → Docker build → push to ACR. CD: deploy to DEV → integration tests → STAGING → manual approval gate → PRODUCTION. Use Variable Groups linked to Key Vault for secrets. Helm charts for K8s deployment. Alternative: GitHub Actions with same flow.
