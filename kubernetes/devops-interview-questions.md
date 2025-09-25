# DevOps Interview Questions and Answers (10+ Years Experience)

This document is organized into Basic, Intermediate, Advanced, and Most Common interview questions. Each question includes a detailed answer and practical code snippets where applicable.

## Basic Questions

**1. What is DevOps?**
DevOps is a cultural and technical movement aimed at unifying software development (Dev) and IT operations (Ops) to shorten the development lifecycle, increase deployment frequency, and improve reliability through automation, collaboration, and observability.

**2. What is Continuous Integration (CI) and Continuous Delivery/Deployment (CD)?**
- CI: Practice of merging code changes frequently, automated builds and tests run on each commit.
- CD: Automating the release process so that validated changes can be deployed to production (Continuous Delivery requires manual approval for production; Continuous Deployment automates everything).

**3. Basic Git commands (examples):**
```bash
git clone https://github.com/user/repo.git
git checkout -b feature-x
git add .
git commit -m "feat: add feature x"
git push origin feature-x
```

**4. How to write a simple Dockerfile for a web app:**
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 3000
CMD ["node", "index.js"]
```

**5. How to run a container and map ports:**
```bash
docker build -t myapp:latest .
docker run -d --name myapp -p 3000:3000 myapp:latest
```

**6. How to set up a simple cron job with systemd timer (Linux):**
- Create `/etc/systemd/system/myjob.service` and `/etc/systemd/system/myjob.timer` and enable the timer.

**7. How to check system logs on Linux:**
```bash
journalctl -u myservice -f
```

## Intermediate Questions

**8. How to create a Jenkins pipeline (Declarative):**
```groovy
pipeline {
  agent any
  stages {
    stage('Checkout') { steps { git 'https://github.com/user/repo.git' } }
    stage('Build') { steps { sh 'make build' } }
    stage('Test') { steps { sh 'make test' } }
    stage('Docker Build & Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'registry-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh 'docker login -u $USER -p $PASS myregistry.com'
          sh 'docker build -t myregistry.com/myapp:${env.BUILD_NUMBER} .'
          sh 'docker push myregistry.com/myapp:${env.BUILD_NUMBER}'
        }
      }
    }
    stage('Deploy') { steps { sh 'kubectl apply -f k8s/deployment.yaml' } }
  }
}
```

**9. GitHub Actions example for CI:**
```yaml
name: CI
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - name: Set up Node
      uses: actions/setup-node@v4
      with:
        node-version: '18'
    - run: npm ci
    - run: npm test
```

**10. Docker Compose example for multi-service app:**
```yaml
version: '3.8'
services:
  web:
    build: ./web
    ports:
      - "8080:80"
    depends_on:
      - api
  api:
    build: ./api
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgres://user:pass@db:5432/appdb
  db:
    image: postgres:15
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
```

**11. Kubernetes Deployment manifest (simple):**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myregistry.com/myapp:latest
        ports:
        - containerPort: 3000
```

**12. Ansible playbook example to install Nginx:**
```yaml
- name: Install Nginx
  hosts: webservers
  become: yes
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
    - name: Ensure nginx is running
      service:
        name: nginx
        state: started
        enabled: true
```

**13. Terraform example to create an AWS EC2 instance:**
```hcl
provider "aws" {
  region = "us-east-1"
}
resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.micro"
  tags = { Name = "web-server" }
}
```

## Advanced Questions

**14. How do you design a blue/green or canary release pipeline?**
- Use feature flags and traffic shifting (service mesh like Istio, or ingress + weighted routing).
- Example: Istio VirtualService to split traffic 90/10 for canary.

**15. How do you manage secrets in production?**
- Use a secret manager (HashiCorp Vault, AWS Secrets Manager) with short-lived credentials and least privilege.
- In Kubernetes use external secrets operator or Vault injector.

**16. Terraform state management best practices:**
- Use remote backend (S3/GCS), lock state (DynamoDB), enable encryption and IAM controls.

**17. Observability stack example (Prometheus + Grafana):**
- Run Prometheus to scrape metrics, Grafana for dashboards, and Alertmanager for alerts. Use kube-state-metrics and node-exporter.

**18. How to implement GitOps?**
- Use Flux or Argo CD to sync Git repositories to cluster state. All changes go via PRs and are reconciled by the operator.

**19. Example: Idempotent Ansible role structure**
- Roles: tasks/main.yml, handlers/main.yml, defaults/main.yml, templates/, files/

**20. CI/CD security hardening practices:**
- Use least privileged pipeline service accounts, scan dependencies, sign artifacts, enforce branch protections and mandatory reviews.

## Most Common DevOps Interview Questions

**21. What is Infrastructure as Code (IaC)?**
IaC is the practice of provisioning and managing infrastructure using machine-readable configuration files, such as Terraform or CloudFormation, making infrastructure reproducible and version-controlled.

**22. Difference between containers and virtual machines?**
- Containers share the host OS kernel and are lightweight; VMs include a guest OS and are heavier.

**23. How do you rollback a failed deployment?**
- Use deployment strategies like rolling updates with health checks, or use the orchestration tool's rollback (Kubernetes `kubectl rollout undo`).

**24. What is a Service Mesh?**
- A service mesh (Istio, Linkerd) manages service-to-service communication, providing observability, traffic management, security (mutual TLS), and retries.

**25. How to monitor application performance in production?**
- Collect metrics (Prometheus), traces (Jaeger/OpenTelemetry), and logs (ELK/EFK), and create dashboards and alerts.

**26. What is immutable infrastructure?**
- Immutable infrastructure replaces servers instead of mutating them; deploy new instances with new configurations and retire old ones.

**27. How do you ensure high availability for an application?**
- Use multiple replicas, spread across availability zones, auto-scaling, health checks, and managed load balancers.

**28. How to secure SSH access to servers?**
- Use key-based authentication, disable password login, use bastion hosts, and use central auth (LDAP/SSO) and MFA.

**29. What is CI pipeline caching and why is it important?**
- Caching dependencies/artifacts speeds up builds by avoiding re-downloads. Examples: Docker layer cache, npm/yarn cache, Maven repository cache.

**30. How to handle database schema migrations in CI/CD?**
- Use migration tools (Flyway, Liquibase) and run migrations as part of deployment with schema versioning and rollback strategies.

---

This file has been created as `devops-interview-questions.md` in your workspace. You can ask for additional focus areas (security, networking, cloud provider specifics) or request printable PDF or slide deck versions.

## 100 Most Common DevOps Interview Questions and Answers

Below are 100 commonly asked DevOps interview questions with concise but thorough answers. Use these to prepare for senior-level interviews.

1. What are the core principles of DevOps?
  - Culture of collaboration, automation, measurement, sharing, continuous improvement, and feedback loops.

2. Explain CI/CD and its benefits.
  - Continuous Integration automates building/testing on commit. Continuous Delivery/Deployment automates releasing code, reducing time-to-market and risk.

3. How do you choose between Continuous Delivery and Continuous Deployment?
  - CD (Delivery) if you need manual approvals for production; Continuous Deployment if you want fully automated releases with strong test coverage.

4. What is GitFlow and when to use it?
  - GitFlow is a branching model with feature, develop, release, and hotfix branches. Use it for complex release cycles; may be heavy for continuous deployment.

5. How does trunk-based development differ from GitFlow?
  - Trunk-based uses short-lived feature branches or direct commits to main/trunk, favors frequent integrates and simplifies CI/CD.

6. What is a build artifact?
  - A build artifact is the packaged output from a build (e.g., JAR, WAR, Docker image) that will be deployed to environments.

7. What is an artifact repository? Example tools.
  - A place to store build artifacts (Nexus, Artifactory, GitHub Packages).

8. How do you manage environment differences (dev/test/prod)?
  - Use configuration management, environment variables, templating (Helm), and feature flags.

9. How to securely store pipeline credentials?
  - Use secret stores, encrypted credentials in CI tools, or integrate with Vault/secret manager.

10. How to test infrastructure code?
  - Unit tests (terratest), static checks (tflint, checkov), integration tests in ephemeral environments, and policy checks.

11. What is idempotence in configuration management?
  - Running the same configuration repeatedly results in the same state; Ansible, Chef, and Puppet aim for idempotence.

12. How do you perform database migrations safely?
  - Backups, zero-downtime migration strategies (backfill, backwards-compatible changes), migration tools, and feature toggles.

13. What are deployment strategies (list and brief)?
  - Rolling update, Blue-Green, Canary, Recreate, A/B testing.

14. How to implement Canary deployments?
  - Use traffic routing (service mesh, ingress) to send a small percentage to new version and monitor before increasing rollout.

15. What is a health check and why is it important?
  - Liveness/readiness probes check app health; ensure traffic only goes to healthy pods and enable self-healing.

16. How to monitor CI/CD pipelines?
  - Monitor build durations, failure rates, queue lengths, and integrate alerts and dashboards.

17. How to troubleshoot a failing build?
  - Check build logs, dependency versions, resource limits, environment differences, and rerun with verbose logging.

18. How do you optimize Docker images?
  - Minimize layers, use multi-stage builds, use slim base images, leverage .dockerignore, and reduce total image size.

19. Explain Docker image layering.
  - Each Dockerfile instruction creates a layer; caching uses layers to speed up builds when preceding layers are unchanged.

20. What is Docker registry and how to run a private one?
  - A registry stores images (Docker Hub, private registry). Run registry image: `docker run -d -p 5000:5000 --name registry registry:2`.

21. What is container orchestration and why is it needed?
  - Manages containers at scale (scheduling, scaling, service discovery). Kubernetes is the leading orchestration platform.

22. Explain the Kubernetes control plane components.
  - API Server, etcd, Controller Manager, Scheduler.

23. What is a Kubernetes Deployment and how does it work?
  - Manages ReplicaSets and ensures desired pod replicas are running. Supports updates and rollbacks.

24. How does Kubernetes scheduling work at a high level?
  - Scheduler watches for unscheduled pods, evaluates node resources/constraints, and binds pods to nodes.

25. What are taints and tolerations?
  - Taints repel pods from nodes; tolerations allow pods to be scheduled on tainted nodes.

26. What are Kubernetes ConfigMaps and Secrets differences?
  - ConfigMaps store non-sensitive config. Secrets store sensitive data (base64 encoded) with RBAC controls and optional encryption.

27. What is Helm and why use it?
  - Helm is a package manager for Kubernetes; templating simplifies deploying complex apps and managing versions.

28. How to handle stateful services in Kubernetes?
  - Use StatefulSets, PersistentVolumes, and stable network identities.

29. What is RBAC in Kubernetes?
  - Role-Based Access Control defines permissions via Roles/ClusterRoles and RoleBindings/ClusterRoleBindings.

30. Explain network policies in Kubernetes.
  - NetworkPolicy defines ingress/egress rules for pods, implementing network-level segmentation.

31. How to secure a Kubernetes cluster?
  - RBAC, PodSecurityPolicies (or Pod Security Admission), network policies, TLS, audit logging, and least privilege.

32. What is a service mesh and what benefits does it bring?
  - Manages service-to-service communication with observability, security, traffic control (mTLS, retries, circuit breaking).

33. How to manage secrets with HashiCorp Vault?
  - Use dynamic secrets, lease management, and inject secrets into workloads via Vault Agent or external-secrets.

34. How to handle logging in a distributed system?
  - Centralize logs with EFK/ELK, use structured logs, correlate via trace IDs, and set retention/alerts.

35. What is distributed tracing and tools used?
  - Traces show requests across services. Tools: Jaeger, Zipkin, OpenTelemetry.

36. How to implement observability (3 pillars)?
  - Metrics (Prometheus), logs (EFK), traces (Jaeger/OpenTelemetry), with dashboards and alerts.

37. How to design alerts to avoid alert fatigue?
  - Use meaningful thresholds, multi-stage alerts, team routing, and tune for noise reduction.

38. What is auto-scaling and how to implement it?
  - Scale based on metrics (HPA) or custom metrics, use cluster autoscaler for node scaling.

39. What is cluster autoscaler?
  - Adds/removes nodes based on pod scheduling needs in cloud environments.

40. How to manage Kubernetes upgrades with minimal downtime?
  - Drain nodes, run upgrades control plane first, then worker nodes; use rolling updates and health checks.

41. What is immutable tagging strategy for Docker images?
  - Tag images with immutable identifiers (commit SHA) to avoid ambiguity and ensure reproducible deployments.

42. What is the 12-factor app and relevance to DevOps?
  - Twelve principles for building cloud-native apps (config, backing services, logs, processes) that simplify DevOps practices.

43. Explain blue/green vs Canary deployments.
  - Blue/Green switches all traffic between two environments; Canary gradually shifts traffic for safer rollouts.

44. How to manage DNS in Kubernetes?
  - CoreDNS provides cluster DNS; use external-dns to sync services with external DNS providers.

45. What is the role of Ingress controllers?
  - Ingress controllers implement routing, TLS termination, and host/path-based routing for external traffic.

46. How to handle secrets rotation?
  - Automate rotation with Vault or cloud secret managers; coordinate application refresh to pick up new credentials.

47. How to perform disaster recovery for Kubernetes?
  - Backup etcd, persistent volumes, and use cluster snapshots, document restore procedures and test regularly.

48. What are GitOps best practices?
  - Keep declarative manifests in Git, use operators to reconcile state, maintain PR-based changes, and use automated rollbacks.

49. How to test Kubernetes manifests?
  - Use kubeval, kube-score, and unit tests (kuttl, kind clusters), plus integration tests in ephemeral environments.

50. What is a runbook and why important?
  - Runbook is step-by-step operational guidance for incidents, ensuring repeatable incident response.

51. How to ensure compliance and governance in IaC?
  - Policy-as-code (OPA/Gatekeeper), terraform compliance, code reviews, audit logs, and immutable pipelines.

52. What is canary analysis and tools?
  - Automated comparison of canary vs baseline metrics to decide rollout; tools: Kayenta (Spinnaker), Flagger.

53. How to secure CI runners and agents?
  - Run in isolated networks, use ephemeral runners, limit permissions, and enforce workspace isolation.

54. What is configuration drift and how to detect/fix it?
  - Drift occurs when runtime configs differ from declared state; detect via drift detection tools and reconcile with automation.

55. How to do capacity planning for Kubernetes clusters?
  - Analyze workloads, resource requests/limits, historical metrics, and size nodes accordingly across zones.

56. Explain Service Discovery patterns.
  - DNS-based discovery, client-side discovery, server-side discovery via load balancers, or service meshes.

57. What is the role of TLS and mTLS in microservices?
  - TLS encrypts traffic; mTLS mutually authenticates client/server to prevent impersonation.

58. How to implement rate limiting for APIs?
  - Use API gateways, ingress controllers or service mesh rate limiting features, token buckets or leaky bucket algorithms.

59. How to do chaos engineering in production?
  - Run controlled experiments (Chaos Monkey, LitmusChaos) to validate resiliency and recovery processes.

60. What is observability-driven SLO/SLI/SLAs?
  - Define Service Level Indicators (metrics), Service Level Objectives (targets), and Service Level Agreements (contracts); monitor and alert accordingly.

61. How to manage multiple cloud providers (multi-cloud) challenges?
  - Abstract infra via Terraform, use cloud-agnostic patterns, centralized logging/monitoring, and handle networking differences.

62. What is the purpose of a bastion host? Security considerations?
  - Bastion gatekeepers admin access to private networks; harden via limited access, session recording, and MFA.

63. How to use SSH certificates instead of keys?
  - Use CA-signed certs for short-lived SSH access with tools like Vault SSH secrets engine.

64. What is immutable infrastructure vs mutable? Pros/cons?
  - Immutable replaces instances; simpler rollback and reproducibility. Mutable allows patching but risks config drift.

65. How to implement centralized secret rotation?
  - Use Vault or cloud secret manager with rotation policies and automated secret distribution.

66. What is a pipeline as code and benefits?
  - Define pipelines in version-controlled files (Jenkinsfile, GitHub Actions, GitLab CI), enabling review, reuse, and traceability.

67. How to handle long-running migrations or background jobs during deployment?
  - Use backwards-compatible schema changes, run migrations in small steps, and use feature flags to toggle functionality.

68. How to detect memory leaks in production?
  - Monitor memory usage, heap dumps, profiling, and use APM tools to correlate traces with resource spikes.

69. What is the difference between readiness and liveness probe?
  - Liveness determines if container should be restarted. Readiness determines if container can receive traffic.

70. How to manage secrets in CI pipelines?
  - Use encrypted secrets feature of CI tools, or retrieve secrets at runtime from vault with short-lived tokens.

71. How to migrate workloads to Kubernetes?
  - Containerize apps, create manifests/Helm charts, migrate storage, and use canary/blue-green strategies.

72. What is the significance of resource requests and limits in Kubernetes?
  - Requests aid scheduling; limits prevent resource abuse; both help cluster stability and fairness.

73. How to debug networking issues in Kubernetes?
  - Check pod logs, `kubectl describe`, `kubectl exec`, CNI plugin status, network policies, and use tools like `tcpdump` in pods.

74. How to handle storage in Kubernetes (CSI drivers)?
  - Use Container Storage Interface drivers for dynamic provisioning and vendor-specific storage solutions.

75. What is ephemeral storage vs persistent storage?
  - Ephemeral tied to pod lifecycle; persistent survives pod restarts and is backed by PVs/PVCs.

76. How to implement pod disruption budgets (PDB)?
  - Set minAvailable or maxUnavailable in PDB to avoid too many simultaneous disruptions during maintenance.

77. What is the role of CNI in Kubernetes?
  - Container Network Interface manages pod networking (e.g., Calico, Flannel, Weave).

78. How to secure container images?
  - Scan images, use minimal base images, sign images (Notary/Cosign), and enforce image provenance policies.

79. What is Infrastructure testing (shift-left) and tools?
  - Test infra code early with terratest, kitchen-terraform, policy checks, and unit tests.

80. How to handle secrets in Kubernetes with external secrets operator?
  - External Secrets sync secrets from external providers into Kubernetes Secrets securely.

81. How to achieve zero-downtime deploys with databases?
  - Use backward-compatible migrations, feature flags, dual-read setups, and rolling schema changes.

82. How to maintain high-quality code in pipelines?
  - Run static analysis, linting, unit tests, integration tests, and enforce code review policies.

83. What is container runtime interface (CRI)?
  - API between kubelet and container runtime (containerd, CRI-O, Docker shim). Kubernetes uses CRI for runtime operations.

84. How to monitor node-level metrics?
  - Use node-exporter, kube-state-metrics, and Prometheus to collect CPU, memory, disk, and network metrics.

85. How to rotate Kubernetes certificates?
  - Use kubeadm certs renew or follow provider-specific docs; test rotation in staging.

86. What is the role of admission controllers in Kubernetes?
  - Enforce policies on object creation/update (validating/mutating webhooks, PodSecurityAdmission).

87. How to manage quotas and limits in namespaces?
  - Use ResourceQuota and LimitRange to control resource consumption per namespace.

88. How to use sidecar pattern effectively?
  - Use sidecars for logging, proxying, or data fetching while keeping application code simple.

89. How to implement backup strategies for stateful workloads?
  - Use volume snapshots, logical backups, and point-in-time restore mechanisms tailored to the datastore.

90. What is drift detection in Terraform and how to reconcile?
  - `terraform plan` shows differences between state and infra; reconcile by applying or importing changes.

91. How to use policy as code (e.g., OPA/Gatekeeper) in CI/CD?
  - Integrate policy checks during PRs and gate merges to block non-compliant changes.

92. How to handle secrets for serverless functions?
  - Use provider secret stores (AWS Secrets Manager, Azure Key Vault) and grant least-privilege IAM roles.

93. What is the role of feature flags in deployments?
  - Decouple release from deployment, enable gradual rollouts, and toggle features without redeploying.

94. How to set up disaster recovery for multi-region deployments?
  - Replicate data across regions, use global load balancers, and implement failover plans and testing.

95. How to identify slow database queries affecting performance?
  - Use APMs, slow query logs, EXPLAIN plans, and monitoring to find and optimize queries.

96. What is the importance of immutable logs?
  - Immutable logs ensure auditability and tamper-evidence for compliance and incident forensics.

97. How to secure build artifacts and pipelines from supply-chain attacks?
  - Sign artifacts, use SBOMs, scan dependencies, restrict external access to build environments.

98. What is a Software Bill of Materials (SBOM)?
  - A list of components and dependencies in a software artifact to improve supply-chain transparency.

99. How to manage secrets access auditing?
  - Use centralized secret stores with audit logs and fine-grained access controls.

100. How to prepare for a DevOps interview at senior level?
  - Understand system design, be ready to discuss trade-offs, know tools deeply, demonstrate incident experience, and practice whiteboard and scenario questions.
