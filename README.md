**AI-Powered Incident Triage & Auto-Remediation for Kubernetes**

An AI-powered incident management system that monitors Kubernetes applications, detects failures, analyzes root causes using Large Language Models (LLMs), and recommends or automates remediation through GitOps pull requests.

The project combines DevOps, Kubernetes, Observability, CI/CD, Infrastructure as Code (IaC), GitOps, and Generative AI to demonstrate a practical, end-to-end incident response solution rather than just an AI chatbot.

**Project Overview**

Modern Kubernetes environments are complex, and troubleshooting application failures often requires significant manual effort.

This project aims to automate incident detection, diagnosis, and remediation using AI.

The system continuously monitors Kubernetes workloads, detects anomalies or failures, collects relevant logs, metrics, and events, and uses an LLM to identify potential root causes.

Based on the analysis, it generates remediation recommendations and creates GitOps pull requests for review and approval.

**Key Objectives**
1. Automated Incident Detection: Detect application failures, resource exhaustion, high latency, and other operational issues.
2. AI-Powered Root Cause Analysis: Use an LLM to analyze logs, metrics, events, and deployment history.
3. RAG-Based Knowledge Retrieval: Leverage runbooks and historical incidents to provide context-aware recommendations.
4. Automated Remediation: Generate configuration fixes and create GitOps pull requests.
5. Human-in-the-Loop Security: Require approval before remediation changes are applied.
6. Observability: Provide visibility into application health, incidents, and remediation activities.
7. Incident Evaluation: Measure diagnosis accuracy using controlled failure scenarios.

**Architecture**
flowchart TD
    A["Kubernetes Application"] --> B["Prometheus / Loki"]
    B --> C["Alertmanager"]
    C --> D["AI Incident Responder (FastAPI)"]

    D --> E["Context Collector"]
    E --> F["Logs, Metrics, Events"]
    E --> G["Pod Details & Deployments"]

    F --> H["LLM Diagnosis"]
    G --> H

    I["Runbooks & Past Incidents"] --> J["Vector Database"]
    J --> H

    H --> K["Root Cause Analysis"]
    K --> L["Recommended Remediation"]
    L --> M["GitOps Pull Request"]

    M --> N["Human Approval"]
    N --> O["Git Repository"]
    O --> P["Argo CD"]
    P --> A

    K --> Q["Slack / Email Notification"]
    M --> Q

**How It Works**
_1. Sample Application_

Deploy a sample microservices application on Kubernetes.

Example components:

FastAPI-based microservices
Redis for caching
PostgreSQL for persistent storage

The application serves as the target environment for monitoring, incident detection, and remediation testing.

_2. Observability_

Deploy an observability stack to collect and visualize application and infrastructure data.

Prometheus: Collects application and Kubernetes metrics.
Grafana: Provides dashboards and visualizations.
Loki: Aggregates and stores application logs.
Alertmanager: Handles alerts and forwards incident notifications.

_3. Automated Alerting_

Alertmanager triggers webhooks when predefined failure conditions are detected.

Example scenarios:

CrashLoopBackOff
High application latency
OOMKilled containers
Increased HTTP 5xx errors
Abnormal CPU or memory consumption

_4. AI-Powered Incident Triage_

A Python-based FastAPI service receives alerts and initiates the incident analysis process.

The AI agent:

Receives the alert payload.
Collects recent application logs.
Retrieves Kubernetes pod events.
Executes read-only Kubernetes diagnostics, such as kubectl describe.
Checks recent deployments and configuration changes.
Builds a structured prompt with the collected context.
Sends the information to the Claude API for analysis.

The agent returns:

Incident summary
Potential root cause
Severity level
Supporting evidence
Recommended remediation steps
Confidence or uncertainty information

_5. RAG-Based Runbook Retrieval_

Integrate Retrieval-Augmented Generation (RAG) to ground AI recommendations in operational knowledge.

The system uses a vector database such as Chroma or pgvector to store:

Kubernetes troubleshooting runbooks
Internal operational documentation
Historical incident reports
Previously validated remediation procedures

During incident analysis, the agent retrieves relevant documents and provides them as context to the LLM.

This helps generate recommendations aligned with documented operational procedures.

_6. GitOps-Based Remediation_

The agent generates configuration changes and opens pull requests against the GitOps repository.

Example remediation scenarios:

Incident	Suggested Remediation
High memory usage	Increase container memory limits after reviewing resource requirements
Bad image deployment	Roll back to a previously validated image tag
High CPU usage	Adjust CPU requests and limits based on workload analysis
Application crash loop	Correct configuration or environment variables
Failed health checks	Review and adjust readiness or liveness probe configuration

Safety controls:

No direct cluster modifications by the AI agent.
Remediation changes are submitted through pull requests.
Human approval is required before merging.
Argo CD applies approved changes.
All remediation activities are logged and auditable.


**Repository Structure**
ai-incident-responder/
│
├── app/                        # Sample microservices application
│   ├── service-a/
│   ├── service-b/
│   └── requirements.txt
│
├── agent/                      # AI incident triage service
│   ├── main.py
│   ├── analyzer.py
│   ├── context_collector.py
│   ├── remediation.py
│   ├── rag.py
│   ├── notifier.py
│   └── requirements.txt
│
├── runbooks/                   # RAG knowledge base
│   ├── crashloop.md
│   ├── oomkilled.md
│   ├── high-latency.md
│   └── deployment-failure.md
│
├── helm/                       # Helm charts
│   ├── app/
│   ├── agent/
│   └── observability/
│
├── gitops/                     # Argo CD manifests
│   ├── applications/
│   └── projects/
│
├── terraform/                  # Infrastructure as Code
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
│
├── .github/
│   └── workflows/              # CI/CD pipelines
│       ├── ci.yml
│       ├── security-scan.yml
│       └── build-push.yml
│
├── chaos/                      # Failure injection scripts
│   ├── memory-stress.sh
│   ├── bad-image.sh
│   └── crash-loop.sh
│
├── docs/                       # Documentation and diagrams
│   ├── architecture.md
│   └── demo.gif
│
├── tests/                      # Unit and integration tests
│
├── Makefile                    # One-command setup and demo
├── .env.example                # Environment variable template
├── .gitignore
└── README.md

**Getting Started**
Prerequisites

Ensure the following tools are installed:
Docker
kubectl
kind or Minikube
Helm
Python 3.10+
Git
Terraform (optional for local setup)
Argo CD CLI (optional)

1. Clone the Repository
git clone https://github.com/<your-username>/ai-incident-responder.git
cd ai-incident-responder

2. Configure Environment Variables

Create a local environment file:
cp .env.example .env
Configure the required values:
CLAUDE_API_KEY=your_api_key
GITHUB_TOKEN=your_github_token
GITHUB_REPOSITORY=your-gitops-repository
SLACK_WEBHOOK_URL=your_slack_webhook
Never commit .env or real API keys to Git.

3. Start the Local Kubernetes Cluster

Using kind:
kind create cluster --name incident-responder
Verify the cluster:
kubectl cluster-info
kubectl get nodes

4. Deploy the Application

Install the sample application using Helm:
helm install sample-app ./helm/app
Verify the deployment:
kubectl get pods
kubectl get services

5. Deploy the Observability Stack
Install the required monitoring components using Helm charts or your configured deployment manifests.
Deploy:
Prometheus
Grafana
Loki
Alertmanager
Verify that metrics and logs are being collected and alerts are configured.

6. Deploy the AI Agent

Build and deploy the AI incident responder:
docker build -t incident-agent:latest ./agent
For a local kind cluster, load the image:
kind load docker-image incident-agent:latest \
  --name incident-responder
Deploy the agent using Helm:
helm install incident-agent ./helm/agent
Configure the required API credentials using Kubernetes Secrets rather than embedding secrets in manifests.

7. Configure GitOps

Install Argo CD and configure the GitOps repository.
Ensure the repository contains the required Kubernetes manifests and application definitions.
Configure the agent to open pull requests in the GitOps repository.

8. Trigger a Test Incident

Run a controlled failure scenario:
kubectl delete pod <sample-app-pod>
Alternatively, use one of the chaos scripts:
bash chaos/crash-loop.sh

Observe the workflow:

Kubernetes reports the failure.
Prometheus and Alertmanager detect and forward the alert.
The AI agent gathers diagnostic context.
The LLM analyzes the incident.
The agent retrieves relevant runbooks.
A diagnosis and remediation recommendation are generated.
A pull request is created, if the proposed fix is eligible.
An authorized reviewer approves the change.
Argo CD synchronizes the approved configuration.
The system verifies whether the incident has been resolved.

CI/CD Pipeline

GitHub Actions automates the application's build, testing, and security validation process.

**Security Considerations**

Security is a core part of the incident response workflow.

The following controls are planned:

Least Privilege: Restrict Kubernetes access to only the resources and operations required by the agent.
Read-Only Diagnostics: Allow the agent to inspect workloads and retrieve logs without direct write access to the cluster.
Human Approval: Require authorized reviewers to approve remediation pull requests.
Secret Management: Store API keys and credentials securely using Kubernetes Secrets or an external secrets manager.
Supply Chain Security: Scan container images and dependencies using Trivy.
Secret Scanning: Use Gitleaks to prevent accidental credential exposure.
Auditability: Maintain records of alerts, AI analyses, proposed changes, approvals, and remediation outcomes.
Prompt Injection Protection: Treat application logs, events, and retrieved documents as untrusted input. Do not allow their contents to override the agent's system instructions or security policies.

**Potential future improvements include:**

Support for multiple LLM providers.
Multi-cluster incident monitoring.
Advanced anomaly detection.
Incident correlation across multiple microservices.
Automated post-remediation health verification.
Historical incident trend analysis.
Cost-aware remediation recommendations.
Integration with incident management platforms such as Jira or ServiceNow.
Policy-based remediation approval workflows.
Enhanced evaluation using a larger incident dataset.
Contributing

Contributions, suggestions, and improvements are welcome.

To contribute:
Fork the repository.
Create a feature branch.
Implement and test your changes.
Submit a pull request with a clear description.
