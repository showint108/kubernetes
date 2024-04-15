# NAMESPACE
Namespaces are a key feature of Kubernetes that allow you to organize and isolate group resources within the same cluster.

### What is a Namespace and What Problem Does it Solve?

**Purpose of Namespaces:**
- **Isolation:** Namespaces provide a mechanism for isolating group resources within the same cluster. This is useful in environments with many users spread across multiple teams or projects, as it prevents naming conflicts about resources like pods, services, deployments, etc.
- **Resource Management:** They allow for management of resource quotas, which limits the amount of CPU, memory, and storage that a group or project can use, preventing one part of the organization from consuming all the cluster resources.
- **Access and Security:** Namespaces help in implementing access policies that restrict who can view or edit resources in specific namespaces.

### How to Effectively Use Namespaces?

**Effective Usage of Namespaces:**
- **Environment Separation:** Use namespaces to separate environments like development, testing, and production within the same cluster.
- **Role-based Access Control (RBAC):** Apply RBAC rules to provide appropriate permissions to different teams according to the namespace they are working in.
- **Resource Monitoring and Limits:** Use namespaces to apply resource quotas and limit ranges to manage compute resources efficiently and prevent over-usage.

### Managing Namespaces: Basic Commands

**Create a Namespace:**

- **Imperative:**
  ```bash
  kubectl create namespace <namespace-name>
  ```

- **Declarative:**
  First, create a YAML file named `namespace.yaml`:
  ```yaml
  apiVersion: v1
  kind: Namespace
  metadata:
    name: <namespace-name>
  ```
  Then, apply it using:
  ```bash
  kubectl apply -f namespace.yaml
  ```

**Delete a Namespace:**
```bash
kubectl delete namespace <namespace-name>
```

**List All Namespaces:**
```bash
kubectl get namespaces
```

**Get Namespace Details:**
```bash
kubectl get namespace <namespace-name> -o yaml
```

**Get Events Related to a Namespace:**
```bash
kubectl get events --namespace <namespace-name>
```

### Working with Resources in a Namespace

**Get Pods in a Namespace:**
```bash
kubectl get pods --namespace <namespace-name>
```

**Change Default Namespace Temporarily in Commands:**
```bash
kubectl get pods --namespace <namespace-name>
```

**Permanently Change Default Namespace for Current Context:**
```bash
kubectl config set-context $(kubectl config current-context) --namespace=<namespace-name>
```

**Refer to Resources in Other Namespaces:**
- Use the fully qualified name: `<resource-name>.<namespace>.<resource-type>.<cluster-domain-name>` for resource.
- Example: `db-service.dev.svc.cluster.local` refers to a service named `db-service` in the `dev` namespace.

**Create Resource Quota for a Namespace:**
- Create a YAML file named `quota.yaml`:
  ```yaml
  apiVersion: v1
  kind: ResourceQuota
  metadata:
    name: example-quota
    namespace: <namespace-name>
  spec:
    hard:
      pods: "10"
      limits.cpu: "4"
      limits.memory: 2Gi
  ```
- Apply it using:
  ```bash
  kubectl apply -f quota.yaml
  ```

### Advanced Namespace Management

**Get All Namespace Resources:**
To see all resources in a specific namespace, you can use:
```bash
kubectl api-resources --verbs=list --namespaced=true | awk '{print $1}' | xargs -n 1 kubectl get --show-kind --ignore-not-found -n <namespace-name>
```

**Namespace Resource Limits:**
- Resource limits are set using ResourceQuotas as shown above. Limits can include CPU, memory, storage, and more.

By understanding and using these commands and concepts, you can manage namespaces in your Kubernetes cluster more effectively, ensuring efficient resource usage and better isolation between different teams or projects.
