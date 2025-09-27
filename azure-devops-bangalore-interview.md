# Azure DevOps Engineer (Bangalore) — Interview Questions & Answers (9-12 yrs)

This set is tailored to the job description you provided: strong Azure DevOps, CI/CD, IaC (Terraform/ARM/Bicep), Docker/Kubernetes, Azure services (App Service, AKS, Functions, Key Vault), monitoring (Azure Monitor, Log Analytics), security/governance, and collaboration.

## How this map aligns to the JD
- CI/CD: pipeline design, YAML pipelines, release strategies.
- IaC: Terraform, ARM/Bicep examples and state management.
- Containers: Docker best practices, ACR, AKS.
- Azure services: App Services, Functions, Key Vault.
- Observability: Azure Monitor, Log Analytics, alerts, dashboards.
- Security/Governance: RBAC, policies, Managed Identity, Key Vault.

---

## Basic Questions

**1. What is Azure DevOps and its core services?**
Azure DevOps is a suite of services including Azure Repos (Git), Azure Pipelines (CI/CD), Azure Artifacts (packages), Azure Boards (work tracking), and Azure Test Plans (test management).

**2. How do you create a simple YAML pipeline in Azure DevOps?**
```yaml
trigger:
  branches:
    include:
      - main
pool:
  vmImage: 'ubuntu-latest'
steps:
- task: UsePythonVersion@0
  inputs:
    versionSpec: '3.9'
- script: |
    pip install -r requirements.txt
    pytest
  displayName: 'Run tests'
```

**3. How do you store secrets in Azure DevOps pipelines?**
- Use Azure Key Vault integration or pipeline variable groups with secrets marked as secret (encrypted at rest). Use service connections and managed identities for higher security.

**4. What is Azure Key Vault and common use-cases?**
Azure Key Vault stores secrets, keys, and certificates, centrally managing access and rotation. Use-cases: DB credentials, TLS certs, API keys, and SSH keys.

**5. How do you push Docker images to Azure Container Registry (ACR)?**
```bash
az acr login --name myacr
docker build -t myacr.azurecr.io/myapp:1.0 .
docker push myacr.azurecr.io/myapp:1.0
```

**6. How to deploy a container image from ACR to AKS?**
- Create Kubernetes deployment manifest referencing `myacr.azurecr.io/myapp:1.0`, ensure AKS has permissions to pull from ACR (managed identity or `az aks update -n <cluster> -g <rg> --attach-acr <acr>`).

**7. What is Infrastructure as Code (IaC)?**
IaC is managing infrastructure via code (Terraform, ARM, Bicep) enabling repeatable and version-controlled provisioning.

**8. How do you authenticate Azure CLI using managed identity?**
- For an Azure VM or Azure resource with managed identity: `az login --identity` and use assigned RBAC roles.

---

## Intermediate Questions

**9. Provide a simple Terraform snippet to create an Azure resource group and an ACR.**
```hcl
provider "azurerm" {
  features = {}
}
resource "azurerm_resource_group" "rg" {
  name     = "rg-demo"
  location = "eastus"
}
resource "azurerm_container_registry" "acr" {
  name                = "myacrdemo"
  resource_group_name = azurerm_resource_group.rg.name
  sku                 = "Standard"
  admin_enabled       = false
}
```

**10. How to secure Terraform state in Azure?**
- Use Azure Storage Account with container and enable blob locking; use `azurerm` backend with SAS or RBAC and enable encryption.

**11. Show an example Azure Pipelines job to build and push to ACR (using service connection).**
```yaml
trigger:
- main
pool:
  vmImage: ubuntu-latest
steps:
- task: Docker@2
  inputs:
    containerRegistry: 'my-acr-connection'
    repository: 'myapp'
    command: 'buildAndPush'
    Dockerfile: 'Dockerfile'
    tags: '$(Build.BuildId)'
```

**12. How do you implement blue-green deployments in AKS?**
- Deploy green and blue versions as separate deployments and use service selector or ingress rules to switch traffic. Use health probes and gradual switch.

**13. Explain Terraform modules and when to use them.**
- Modules are reusable units of Terraform configuration for encapsulation and reuse across projects (e.g., network module, AKS module).

**14. What is Bicep and why use it over ARM templates?**
- Bicep is a concise, declarative language for ARM that offers simpler syntax, better modularization, and native tooling for authoring Azure deployments.

**15. Describe Azure Monitor components.**
- Metrics, Logs (Log Analytics), Alerts, Application Insights for APM, and Workbooks for dashboards.

**16. How to write a Kusto Query to get error logs from Log Analytics for the last 1 hour?**
```
AppTraces
| where TimeGenerated > ago(1h)
| where SeverityLevel == "Error"
| sort by TimeGenerated desc
```

**17. How do you configure AKS to use Azure AD for cluster authentication?**
- Enable AKS Azure AD integration on cluster creation with `--enable-aad` and configure server/app IDs and admin groups.

**18. Describe rolling update strategy in Kubernetes and relevant fields in Deployment spec.**
- `spec.strategy.type: RollingUpdate` and fields `maxUnavailable` and `maxSurge` control the rollout.

**19. How to enable diagnostic logs for App Service and send them to Log Analytics?**
- Turn on App Service diagnostics and configure Log Analytics workspace under Diagnostics settings or use ARM/Bicep/Terraform to configure diagnostic settings.

**20. How do you secure AKS nodes and workloads?**
- Use node pools with minimal privileges, enable AKS managed identities, use network policies, Pod Security policies (or PodSecurity admission), and image scanning.

---

## Advanced Questions

**21. How to design a secure CI/CD workflow in Azure DevOps?**
- Use service principals with least privilege, store secrets in Key Vault, use pipeline approvals, enforce branch policies, run secret scanning, and use ephemeral agents where possible.

**22. How to integrate Azure Key Vault with Azure Pipelines?**
- Use the Key Vault task or variable groups linked to Key Vault for fetching secrets during pipeline runtime.

**23. Demonstrate an ARM/Bicep snippet to create a Key Vault and add an access policy.**
```bicep
resource kv 'Microsoft.KeyVault/vaults@2021-10-01' = {
  name: 'myKeyVault'
  location: resourceGroup().location
  properties: {
    sku: { name: 'standard' }
    tenantId: subscription().tenantId
    accessPolicies: []
  }
}
```

**24. How to rotate secrets stored in Key Vault automatically?**
- Use Key Vault's rotation policy and integrate with events or Azure Functions to trigger rotation, or use managed identities and Key Vault's rotation features for supported secrets.

**25. How to secure communication between services in AKS?**
- Use mTLS via service mesh (Istio/Linkerd) or AAD Pod Identity + network policies and limit ingress points.

**26. How to implement policy and governance in Azure (Examples)?**
- Use Azure Policy to enforce tagging, allowed VM sizes, naming conventions, and deny public IPs. Combine with Management Groups for inheritance.

**27. How to handle secrets in Git repositories?**
- Never commit secrets. Use secret scanning, pre-commit hooks, and rotate any secrets accidentally committed. Prefer environment-based secret injection.

**28. Explain AKS node auto-scaling vs cluster auto-scaler.**
- Kubernetes HPA scales pods. Cluster autoscaler (in AKS) scales node pools based on pending pods and resource requests.

**29. How to troubleshoot slow deployments to AKS?**
- Check image pull times, node resource pressure, readiness probes causing delays, and registry throttling; review events and kubelet logs.

**30. How to implement multi-tenant clusters securely?**
- Use namespaces, ResourceQuota, NetworkPolicy, RBAC, and maybe virtual clusters (vCluster) or separate clusters for strong isolation.

---

## Scenario & Troubleshooting Questions

**31. Scenario: Pipeline failing only on agent but not locally. How do you investigate?**
- Compare agent environment (OS, installed tools), check pipeline logs and versions, replicate agent image locally, ensure correct SDK/tool installation, and check network/proxy settings.

**32. Scenario: AKS pods cannot pull images from ACR after upgrade.**
- Check ACR permissions, managed identity/service principal expiration, `az aks update --attach-acr`, network restrictions and firewall rules, and inspect `kubectl describe pod` for image pull errors.

**33. Scenario: Secrets from Key Vault not resolving in pipeline.**
- Verify service connection permissions, Key Vault access policies, network access (firewall/vnet), and correct secret names/revision in pipeline.

**34. Scenario: High alert volumes from Azure Monitor. How to reduce noise?**n- Tune alert thresholds, use dynamic thresholds, group alerts via action groups, and implement suppression/windowing.

**35. Scenario: A deployment caused a spike in errors. Rollback strategy?**
- Use `kubectl rollout undo` or switch ingress/service to previous stable version (blue/green). Investigate root cause in logs and traces before re-deploying.

***

File created: `azure-devops-bangalore-interview.md` — next: complete remaining tasks (draft intermediate/advanced already created, scenario section populated). Please tell me if you want deeper expansion on any item or exporting to PDF/slide deck.
