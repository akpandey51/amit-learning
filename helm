
# 📝 Helm Interview Cheat Sheet

---

## **1. Helm Basics**

* Helm = **Package Manager for Kubernetes**.
* Helm Chart = **Bundle of YAML templates** for deploying apps.
* Release = Installed instance of a chart.

---

## **2. Helm Chart Structure**

```
mychart/
  Chart.yaml        # Metadata (name, version, description)
  values.yaml       # Default values
  templates/        # Kubernetes manifests (templated)
  charts/           # Dependencies (subcharts)
  NOTES.txt         # Post-install instructions
```

---

## **3. Core Commands**

```bash
# Add repo
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

# Search charts
helm search repo nginx

# Install
helm install myapp bitnami/nginx -f values.yaml

# Upgrade
helm upgrade myapp bitnami/nginx --set replicaCount=3

# Rollback
helm rollback myapp 1

# Uninstall
helm uninstall myapp

# Get release info
helm list
helm status myapp
helm get all myapp
```

---

## **4. Debugging & Validation**

```bash
# Lint chart
helm lint ./mychart

# Render templates (no install)
helm template ./mychart -f values.yaml

# Dry run with debug
helm install myapp ./mychart --dry-run --debug
```

---

## **5. Multi-Environment**

```bash
helm install myapp ./mychart -f values-dev.yaml
helm install myapp ./mychart -f values-prod.yaml
```

👉 Use separate `values-<env>.yaml` files.

---

## **6. Release Management**

```bash
# History of release
helm history myapp

# Rollback automatically if upgrade fails
helm upgrade myapp ./mychart --atomic --timeout 5m
```

---

## **7. Dependencies**

```yaml
# Chart.yaml
dependencies:
  - name: mysql
    version: 8.0.0
    repository: "https://charts.bitnami.com/bitnami"
```

```bash
helm dependency update ./mychart
```

---

## **8. Hooks (Lifecycle Events)**

* `pre-install`, `post-install`, `pre-delete`, `post-upgrade`
* Example: Run DB migration job before app install.

---

## **9. Secrets Management**

* **Don’t store plaintext secrets** in values.yaml.
  Options:
* Helm Secrets plugin + SOPS → `helm secrets install`
* Sealed Secrets / External Secrets Operator
* Pass via CI/CD pipeline → `--set db.password=$DB_PASS`

---

## **10. Best Practices**

✔ Keep charts modular & reusable.
✔ Use `values.yaml` for configs, not hardcoding.
✔ Use `helm lint` before pushing.
✔ Version charts using **semver**.
✔ Store charts in a Helm repo (S3/GCS/GitHub Pages/Harbor/Artifactory).
✔ Prefer `--atomic` for safe deployments.

---

Perfect 👌
This is the **“Whiteboard Scenario Walkthrough” for Helm** – the kind of practical explanation you’ll need when an interviewer says:

*"You need to deploy an application using Helm. Walk me through the steps."*

---

# 🖊️ Helm Whiteboard Walkthrough (Step-by-Step)

---

## **Scenario**

You’re tasked to deploy a **microservice app** (say `payment-service`) on Kubernetes using Helm.

---

## **Step 1 – Create a Chart**

```bash
helm create payment-service
```

* Generates scaffold: `Chart.yaml`, `values.yaml`, `templates/` folder.
* Remove default manifests you don’t need (like extra services).

---

## **Step 2 – Define Metadata**

In `Chart.yaml`:

```yaml
apiVersion: v2
name: payment-service
description: Helm chart for Payment microservice
version: 0.1.0
appVersion: "1.0.0"
```

---

## **Step 3 – Configure Values**

In `values.yaml`:

```yaml
replicaCount: 3

image:
  repository: myregistry/payment-service
  tag: "1.0.0"

service:
  type: ClusterIP
  port: 8080

resources:
  requests:
    cpu: 200m
    memory: 256Mi
  limits:
    cpu: 500m
    memory: 512Mi
```

👉 You’ll maintain **values-dev.yaml**, **values-prod.yaml** for different environments.

---

## **Step 4 – Write Templates**

In `templates/deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          ports:
            - containerPort: {{ .Values.service.port }}
          readinessProbe:
            httpGet:
              path: /health
              port: {{ .Values.service.port }}
            initialDelaySeconds: 5
            periodSeconds: 10
```

👉 Notice use of **templating** (`{{ }}`) and **probes** for zero-downtime.

---

## **Step 5 – Validate Chart**

```bash
helm lint ./payment-service
helm template ./payment-service -f values-dev.yaml
```

👉 This ensures syntax is good and renders manifests locally.

---

## **Step 6 – Deploy to Cluster**

```bash
helm install payment ./payment-service -f values-dev.yaml
```

👉 Creates release **payment**.
Check status:

```bash
helm status payment
kubectl get pods,svc
```

---

## **Step 7 – Upgrade Application**

If a new image version is released:

```bash
helm upgrade payment ./payment-service --set image.tag=1.1.0
```

👉 Ensures rolling update with no downtime.

---

## **Step 8 – Rollback if Needed**

If something breaks:

```bash
helm rollback payment 1
```

👉 Restores release to revision `1`.

---

## **Step 9 – Automate via CI/CD**

* **CI:** Package chart & push to Helm repo.

  ```bash
  helm package ./payment-service
  helm push payment-service-0.1.0.tgz my-repo
  ```
* **CD:** Deploy using `helm upgrade --install` in Jenkins/GitLab/Azure DevOps.
* Use `--atomic --timeout 5m` for safe upgrades.

---

## **Step 10 – Best Practices in Explanation**

* Separate charts per microservice (unless tightly coupled → subcharts).
* Manage secrets with Vault/SealedSecrets (not plain values.yaml).
* Use GitOps (ArgoCD/Flux) for continuous reconciliation.
* Version control Helm charts (semver).
* Test charts with `helm test` jobs.

---

🎯 **How to Present in Interview**

* Draw the Helm workflow (Chart → Rendered YAML → K8s API → Release).
* Walk step by step: **Create → Configure → Template → Validate → Install → Upgrade → Rollback → Automate**.
* Always mention **zero-downtime deployment**, **secrets handling**, and **CI/CD integration** → shows lead-level thinking.

---

Would you like me to also prepare a **visual diagram (Helm workflow + CI/CD + rollback)** that you can use as a mental map in interviews?
