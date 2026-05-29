# Kubernetes (K8s) & kubectl Command Reference

This reference sheet covers essential `kubectl` and Kubernetes commands, grouped by logical category.

---

## 📌 1. Cluster Information & Configuration
Manage cluster context, configuration, and view cluster-wide information.

| Command | Description |
| :--- | :--- |
| `kubectl cluster-info` | Display addresses of the master and services. |
| `kubectl version --short` | Print the client and server Kubernetes versions. |
| `kubectl api-resources` | List all supported API resource types on the server. |
| `kubectl api-versions` | List the supported API versions on the server. |
| `kubectl config view` | Display merged `kubeconfig` settings. |
| `kubectl config get-contexts` | List all available contexts. |
| `kubectl config current-context` | Display the current active context. |
| `kubectl config use-context <context-name>` | Switch the active context. |
| `kubectl config set-context --current --namespace=<namespace>` | Set default namespace for the current context. |
| `kubectl get nodes` | List all nodes in the cluster with status. |
| `kubectl get nodes -o wide` | List nodes with more details (IP, OS, kernel version). |
| `kubectl describe node <node-name>` | Display detailed status and resources of a node. |
| `kubectl top node` | Show CPU and Memory usage of nodes (requires metrics-server). |

---

## 🏷️ 2. Namespaces
Isolate and partition resources within a single cluster.

*   **List Namespaces:**
    ```bash
    kubectl get namespaces
    # or short:
    kubectl get ns
    ```
*   **Create Namespace:**
    ```bash
    kubectl create namespace <namespace-name>
    ```
*   **Describe Namespace:**
    ```bash
    kubectl describe namespace <namespace-name>
    ```
*   **Delete Namespace:** (Caution: This deletes all resources in the namespace!)
    ```bash
    kubectl delete namespace <namespace-name>
    ```

---

## 📦 3. Resource CRUD (Create, Read, Update, Delete)
Commands for creating and managing resources imperatively and declaratively.

### Declarative Management (Recommended)
*   **Create or update resources from file:**
    ```bash
    kubectl apply -f <filename.yaml>
    ```
*   **Apply all configurations in a directory:**
    ```bash
    kubectl apply -f ./directory/
    ```
*   **Delete resources defined in file:**
    ```bash
    kubectl delete -f <filename.yaml>
    ```

### Imperative Creation
*   **Run a Pod (Quick test):**
    ```bash
    kubectl run nginx-pod --image=nginx --restart=Never
    ```
*   **Create a Deployment:**
    ```bash
    kubectl create deployment nginx-deploy --image=nginx --replicas=3
    ```
*   **Create a Service (Expose Deployment):**
    ```bash
    kubectl expose deployment nginx-deploy --port=80 --target-port=80 --type=ClusterIP
    ```

### Listing & Viewing Resources
*   **Get all resources in the current namespace:**
    ```bash
    kubectl get all
    ```
*   **Get resources in all namespaces:**
    ```bash
    kubectl get pods -A
    # or:
    kubectl get pods --all-namespaces
    ```
*   **Get resources with watch mode (live updates):**
    ```bash
    kubectl get pods -w
    ```
*   **Describe resource details:**
    ```bash
    kubectl describe pod <pod-name>
    kubectl describe deployment <deployment-name>
    ```

---

## 🌐 4. Networking & Services
Manage access to workloads and control ingress/egress.

*   **List Services:**
    ```bash
    kubectl get services
    # or short:
    kubectl get svc
    ```
*   **Expose NodePort or LoadBalancer:**
    ```bash
    kubectl expose deployment <deployment-name> --type=NodePort --port=<port>
    kubectl expose deployment <deployment-name> --type=LoadBalancer --port=<port>
    ```
*   **Port-forwarding (Access internal resources locally):**
    ```bash
    # Forward local port 8080 to Pod's port 80
    kubectl port-forward pod/<pod-name> 8080:80

    # Forward local port 8080 to Service's port 80
    kubectl port-forward svc/<service-name> 8080:80
    ```
*   **List Ingress resources:**
    ```bash
    kubectl get ingress
    ```

---

## 🔄 5. Scaling, Rollouts & Updates
Manage replica counts and software deployment versions.

### Scaling Workloads
*   **Scale a deployment to 5 replicas:**
    ```bash
    kubectl scale deployment <deployment-name> --replicas=5
    ```
*   **Autoscale deployment (Horizontal Pod Autoscaler):**
    ```bash
    kubectl autoscale deployment <deployment-name> --min=2 --max=10 --cpu-percent=80
    ```

### Updates & Rollouts
*   **Update image on deployment (rolling update):**
    ```bash
    kubectl set image deployment/<deployment-name> <container-name>=<new-image>:<tag>
    # Example:
    kubectl set image deployment/bun-web-deployment bun-web-app=oven/bun:1.1-alpine
    ```
*   **Check rollout status:**
    ```bash
    kubectl rollout status deployment/<deployment-name>
    ```
*   **View rollout history:**
    ```bash
    kubectl rollout history deployment/<deployment-name>
    ```
*   **Rollback to previous version:**
    ```bash
    kubectl rollout undo deployment/<deployment-name>
    ```
*   **Rollback to a specific revision:**
    ```bash
    kubectl rollout undo deployment/<deployment-name> --to-revision=2
    ```
*   **Restart a deployment (triggers rolling restart):**
    ```bash
    kubectl rollout restart deployment/<deployment-name>
    ```

---

## 🛠️ 6. Debugging & Troubleshooting
Tools for diagnostics, log analysis, and interactive container execution.

### Viewing Logs
*   **Print logs for a pod:**
    ```bash
    kubectl logs <pod-name>
    ```
*   **Stream logs (follow):**
    ```bash
    kubectl logs -f <pod-name>
    ```
*   **Print logs for a specific container in a multi-container pod:**
    ```bash
    kubectl logs <pod-name> -c <container-name>
    ```
*   **Print logs with timestamps:**
    ```bash
    kubectl logs <pod-name> --timestamps
    ```
*   **Print logs for the last 10 minutes:**
    ```bash
    kubectl logs <pod-name> --since=10m
    ```
*   **Print previous container logs (if container crashed):**
    ```bash
    kubectl logs <pod-name> --previous
    ```

### Running Commands & Shells
*   **Execute command inside container:**
    ```bash
    kubectl exec <pod-name> -- ls -la
    ```
*   **Run interactive bash/sh session inside container:**
    ```bash
    kubectl exec -it <pod-name> -- sh
    # or:
    kubectl exec -it <pod-name> -- bash
    ```

### Monitoring & Events
*   **List cluster events sorted by time:**
    ```bash
    kubectl get events --sort-by='.metadata.creationTimestamp'
    ```
*   **Resource utilization of Pods (metrics-server required):**
    ```bash
    kubectl top pod
    ```

---

## 🗄️ 7. Configuration & Storage
Managing configurations, secrets, and persistent storage.

| Resource | Short Name | Get Command |
| :--- | :--- | :--- |
| **ConfigMaps** | `cm` | `kubectl get configmaps` |
| **Secrets** | - | `kubectl get secrets` |
| **Persistent Volumes** | `pv` | `kubectl get pv` |
| **Persistent Volume Claims** | `pvc` | `kubectl get pvc` |
| **Storage Classes** | `sc` | `kubectl get sc` |

*   **Create ConfigMap from file/literal:**
    ```bash
    kubectl create configmap <cm-name> --from-literal=key1=val1
    kubectl create configmap <cm-name> --from-file=<path-to-file>
    ```
*   **Create Generic Secret from literal:**
    ```bash
    kubectl create secret generic <secret-name> --from-literal=password=my-super-secret
    ```
*   **Decode a Secret value (using bash/base64):**
    ```bash
    kubectl get secret <secret-name> -o jsonpath="{.data.password}" | base64 --decode
    ```

---

## ⚡ 8. Advanced kubectl Options & Tricks
Helper flags, alternative outputs, and formatting hacks.

*   **Dry-run (generate YAML without applying to cluster):**
    ```bash
    kubectl run mypod --image=nginx --dry-run=client -o yaml > pod.yaml
    ```
*   **Diff local manifest against running cluster state:**
    ```bash
    kubectl diff -f <filename.yaml>
    ```
*   **Get resource in custom JSONPath formatting:**
    ```bash
    # Get internal IPs of all nodes
    kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="InternalIP")].address}'
    ```
*   **Explain resource fields (interactive documentation):**
    ```bash
    kubectl explain pods.spec.containers
    ```
*   **Force delete a pod immediately (grace-period=0):**
    ```bash
    kubectl delete pod <pod-name> --grace-period=0 --force
    ```
