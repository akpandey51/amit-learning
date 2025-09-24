# Kubernetes Interview Questions and Answers (10+ Years Experience)

## Basic Questions

**1. What is Kubernetes?**
Kubernetes is an open-source container orchestration platform for automating deployment, scaling, and management of containerized applications.

**2. What are the main components of Kubernetes architecture?**
- Master Node: API Server, Controller Manager, Scheduler, etcd
- Worker Node: Kubelet, Kube Proxy, Container Runtime

**3. How do you deploy an application in Kubernetes?**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```
Apply with:
```bash
kubectl apply -f deployment.yaml
```

## Intermediate Questions

**4. How do you expose a deployment as a service?**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
```

**5. How do you perform rolling updates in Kubernetes?**
- Update the image in the Deployment spec and apply changes:
```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.22
```

**6. How do you rollback a deployment?**
```bash
kubectl rollout undo deployment/nginx-deployment
```

**7. How do you scale a deployment?**
```bash
kubectl scale deployment/nginx-deployment --replicas=5
```

**8. How do you check the status of pods and deployments?**
```bash
kubectl get pods
kubectl get deployments
kubectl describe pod <pod-name>
```

## Advanced Questions

**9. How do you use ConfigMaps and Secrets?**
- ConfigMap example:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: default
data:
  DB_HOST: db.example.com
  DB_PORT: "5432"
```
- Secret example:
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  password: cGFzc3dvcmQ=
```

**10. How do you mount a volume in a pod?**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - name: test-container
    image: busybox
    volumeMounts:
    - name: test-volume
      mountPath: /data
  volumes:
  - name: test-volume
    hostPath:
      path: /tmp/data
      type: Directory
```

**11. How do you use liveness and readiness probes?**
```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 3
  periodSeconds: 3
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 3
  periodSeconds: 3
```

**12. How do you restrict resource usage for a pod?**
```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

**13. How do you perform zero-downtime deployments?**
- Use rolling updates and readiness probes to ensure new pods are ready before terminating old ones.

**14. How do you troubleshoot a failing pod?**
```bash
kubectl logs <pod-name>
kubectl describe pod <pod-name>
kubectl exec -it <pod-name> -- /bin/sh
```

**15. How do you secure a Kubernetes cluster?**
- Use RBAC, Network Policies, Secrets, audit logging, and restrict API access.

**16. How do you use namespaces?**
```bash
kubectl create namespace dev
kubectl get pods --namespace=dev
```

**17. How do you upgrade Kubernetes cluster components?**
- Use kubeadm, managed services, or cloud provider tools. Upgrade master nodes first, then worker nodes.

**18. How do you use Helm for package management?**
```bash
helm install myapp stable/nginx
helm upgrade myapp stable/nginx
helm rollback myapp 1
```

**19. How do you implement auto-scaling in Kubernetes?**
- Use Horizontal Pod Autoscaler:
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

**20. How do you monitor and log Kubernetes clusters?**
- Use Prometheus, Grafana, ELK stack, Fluentd, and native Kubernetes metrics.

## Scenario-Based Questions

**21. Scenario: Blue-Green Deployment in Kubernetes**

*Question:* How would you implement a blue-green deployment strategy in Kubernetes?

*Answer:* Deploy two separate deployments (blue and green) and use a Service to switch traffic between them. Example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-blue
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: myapp
        version: blue
    spec:
      containers:
      - name: app
        image: myapp:blue
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-green
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: myapp
        version: green
    spec:
      containers:
      - name: app
        image: myapp:green
---
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
    version: blue # Switch to green for cutover
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

**22. Scenario: Debugging CrashLoopBackOff Pods**

*Question:* A pod is stuck in CrashLoopBackOff. How do you troubleshoot?

*Answer:*
- Check logs: `kubectl logs <pod-name>`
- Describe pod: `kubectl describe pod <pod-name>`
- Check events for resource issues or misconfiguration.
- Run an interactive shell if possible: `kubectl exec -it <pod-name> -- /bin/sh`
- Review readiness/liveness probes and resource limits.

**23. Scenario: Securing Sensitive Data**

*Question:* How do you securely inject sensitive data (like passwords) into a pod?

*Answer:*
- Use Kubernetes Secrets and mount them as environment variables or files.
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  password: cGFzc3dvcmQ=
---
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:latest
    env:
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: password
```

**24. Scenario: Multi-Tenancy with Namespaces**

*Question:* How do you implement multi-tenancy in Kubernetes?

*Answer:*
- Use namespaces to isolate resources for different teams or projects.
- Apply RBAC policies to restrict access.
```bash
kubectl create namespace team-a
kubectl create namespace team-b
kubectl apply -f rbac-team-a.yaml --namespace=team-a
```

**25. Scenario: Autoscaling Based on Custom Metrics**

*Question:* How do you autoscale pods based on custom application metrics?

*Answer:*
- Use the Kubernetes Metrics API and Prometheus Adapter to expose custom metrics.
- Configure HorizontalPodAutoscaler to use these metrics.
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: custom-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Pods
    pods:
      metric:
        name: custom_requests_per_second
      target:
        type: AverageValue
        averageValue: 100
```

## Additional Theoretical Kubernetes Questions

**26. What is the role of etcd in Kubernetes?**
Etcd is a distributed key-value store used by Kubernetes to store all cluster data, including configuration, state, and metadata. It provides consistency and high availability for the cluster.

**27. What is a Kubernetes Pod?**
A Pod is the smallest deployable unit in Kubernetes, representing a single instance of a running process. Pods can contain one or more containers that share storage, network, and a specification for how to run the containers.

**28. What is a ReplicaSet?**
A ReplicaSet ensures that a specified number of pod replicas are running at any given time. It automatically replaces failed pods and scales the number of pods as needed.

**29. What is a DaemonSet?**
A DaemonSet ensures that a copy of a pod runs on all (or some) nodes in the cluster. It is commonly used for background tasks like log collection or monitoring.

**30. What is a StatefulSet?**
A StatefulSet is used to manage stateful applications. It provides stable, unique network identifiers and persistent storage for each pod, supporting ordered deployment and scaling.

**31. What is a Service in Kubernetes?**
A Service is an abstraction that defines a logical set of pods and a policy by which to access them. Services enable communication between different components and provide load balancing.

**32. What is a Namespace in Kubernetes?**
A Namespace is a way to divide cluster resources between multiple users or teams. It provides scope for names and helps organize resources in large clusters.

**33. What is RBAC in Kubernetes?**
Role-Based Access Control (RBAC) is a method for regulating access to Kubernetes resources based on the roles of individual users or groups. It uses roles and role bindings to define permissions.

**34. What is a Kubernetes Ingress?**
Ingress is an API object that manages external access to services in a cluster, typically HTTP. It provides routing, SSL termination, and virtual hosting.

**35. What is a Node in Kubernetes?**
A Node is a physical or virtual machine on which Kubernetes runs pods. Each node contains the necessary services to run pods and is managed by the master components.

**36. What is a Controller in Kubernetes?**
A Controller is a control loop that watches the state of the cluster and makes changes to achieve the desired state. Examples include Deployment Controller, ReplicaSet Controller, and Job Controller.

**37. What is a Job in Kubernetes?**
A Job creates one or more pods and ensures that a specified number of them successfully terminate. It is used for batch processing and one-off tasks.

**38. What is a PersistentVolume (PV) and PersistentVolumeClaim (PVC)?**
A PersistentVolume is a piece of storage in the cluster, while a PersistentVolumeClaim is a request for storage by a user. PVCs allow pods to use persistent storage independent of the pod lifecycle.

**39. What is a Taint and Toleration in Kubernetes?**
Taints are applied to nodes to repel certain pods, while tolerations are applied to pods to allow them to be scheduled on tainted nodes. This helps control pod placement.

**40. What is the difference between Deployment and StatefulSet?**
Deployment is used for stateless applications, providing scaling and rolling updates. StatefulSet is used for stateful applications, providing stable identities and persistent storage.

---
This file covers Kubernetes interview questions and answers for senior roles, with code examples from basic to advanced.
