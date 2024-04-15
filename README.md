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

**Get All Namespace Resources:**
To see all resources in a specific namespace, you can use:
```bash
kubectl api-resources --verbs=list --namespaced=true | awk '{print $1}' | xargs -n 1 kubectl get --show-kind --ignore-not-found -n <namespace-name>
```

# IMPERATIVE VS DECLERATIVE

### Imperative vs Declarative Approaches in Kubernetes

#### Imperative Commands

**Why are commands written with `kubectl` called imperative?**
- Imperative commands in Kubernetes directly specify the steps required to achieve a desired state. They focus on performing operations (create, delete, update) directly on resources and are akin to giving direct commands to achieve a result.
- Example: `kubectl create deployment nginx --image=nginx`

#### Declarative Management

**Why are YAML files called declarative?**
- Declarative configuration specifies the desired state of the resource, and Kubernetes works to ensure that the actual state matches the desired state automatically. It is more about what you want as the final outcome rather than how you get there.
- Example: Using a YAML file to specify that a deployment should exist with a specific configuration, and Kubernetes maintains this state.

### Creating Resources

**How to create a resource using the imperative way?**
- Example: Creating a deployment imperatively:
  ```bash
  kubectl create deployment nginx --image=nginx
  ```

**How to create a resource using the declarative way?**
- Example: Creating a deployment declaratively:
  1. Write a YAML file named `nginx-deployment.yaml`:
     ```yaml
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: nginx
     spec:
       selector:
         matchLabels:
           app: nginx
       replicas: 2
       template:
         metadata:
           labels:
             app: nginx
         spec:
           containers:
           - name: nginx
             image: nginx
             ports:
             - containerPort: 80
     ```
  2. Apply the configuration:
     ```bash
     kubectl apply -f nginx-deployment.yaml
     ```

### Preferences in Usage

**Why is the declarative way preferred?**
- **Autocorrection:** The declarative approach continuously corrects any divergence between the desired state specified in YAML files and the actual state of the cluster.
- **Idempotency:** Applying the same declarative configuration multiple times does not result in errors or unintended effects, which can happen with some imperative commands if used repeatedly.
- **Version Control:** Declarative files can be version-controlled, providing a history of changes and the ability to revert or apply configurations across different environments.

### Why Certain Commands are Considered Imperative

**Why are `kubectl replace`, `kubectl create`, and `kubectl delete` considered imperative?**
- These commands explicitly tell Kubernetes what to do, often specifying both the action and the object. There's no automatic error correction or state management—if something is wrong after execution, these commands don't attempt to fix the issue on their own.

### Differences in Commands

**`kubectl apply` vs `kubectl create`:**
- `kubectl create`: Creates a resource. It fails if the resource already exists.
- `kubectl apply`: Applies a configuration to a resource. It creates the resource if it does not exist, and updates the resource if it does exist, making it suitable for both creating and updating resources.

**`kubectl apply` vs `kubectl replace`:**
- `kubectl replace`: Deletes and re-creates the resource. It requires a complete resource definition.
- `kubectl apply`: Updates the resource with changes defined in the YAML file, without needing a complete definition. It is safer for updates as it doesn't require deleting the resource.

### Other Relevant Commands and Concepts

**What is a manifest file?**
- A manifest file in Kubernetes is a YAML or JSON file that defines one or more resources. It specifies the desired state of the resources that Kubernetes should maintain.

**How does `kubectl edit` work?**
- `kubectl edit` opens the configured editor (set via `EDITOR` environmental variable), allowing you to modify the live configuration of a specified resource directly. It applies the changes on saving and exiting the editor.

**How does `kubectl replace` work?**
- `kubectl replace -f resource.yaml` replaces a resource based on a YAML or JSON file. The file must include a full definition of the resource, which replaces the existing resource.

**How does `kubectl apply -f /path/to/dir` work?**
- This command applies all YAML configurations found in the specified directory to resources in the cluster. It's useful for managing multiple resources or complex configurations.

**How does `kubectl expose` work?**
- It creates a new service that exposes an existing resource (like pods or deployments) to the network. The service type and exposed ports are specified in the command.
- Example:
  ```bash
  kubectl expose deployment nginx --port=80 --dry-run=client -o yaml
  ```
  This generates the YAML for a service exposing the `nginx` deployment on port 80, without creating the service (`--dry-run=client`).

**Difference between `--dry-run=client`, `--dry-run=server`, and `--dry-run`:**

dry-run=client`: Client-side simulation; the command is processed but not sent to the server.
- `--dry-run=server`: Server-side simulation; the server processes the command without creating the resource.
- `--dry-run`: In older kubectl versions, equivalent to `--dry-run=client`.

**What is the `-o` flag used for? What are the possible values?**
- The `-o` (output) flag specifies the format in which to output the details of a command's result.
- Possible values include `json`, `yaml`, `name`, `wide`, `custom-columns`, and more, allowing for different views and data handling of the command outputs.
