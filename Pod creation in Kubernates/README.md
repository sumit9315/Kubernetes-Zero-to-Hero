**Kubernetes in Production: Transcript (Formatted)**

---

🧠 **Day 33: Deploy Your First Application in Kubernetes**






### Why Kubernetes Over Docker?

* Kubernetes is **clustered**, offers **auto-scaling**, **auto-healing**, and **enterprise-level control**.
* Instead of deploying individual containers like in Docker, Kubernetes uses **pods**.

---

### What is a Pod?

A **pod** is the **lowest-level Kubernetes object** and acts as a **wrapper** around one or more containers.

* In Docker: you run a container using `docker run`
* In Kubernetes: you define the container behavior in a **YAML file** (e.g., `pod.yaml`)

### Docker Command Equivalent:

```bash
docker run -d nginx:1.14.2 --name nginx -p 80:80
```

### Kubernetes Pod YAML Example:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

This YAML describes how to run the container, similar to the Docker command above.

---

### Why Use YAML in Kubernetes?

* Kubernetes supports **declarative configuration**.
* Everything (Pods, Deployments, Services, etc.) is defined in **YAML files**.

You don't need to memorize these YAMLs—most engineers refer to official docs or templates.

---

### Multi-container Pods (Advanced)

* Most pods have **one container**, but advanced use cases may use **sidecars** or **init containers**.
* Containers in the same pod share:

  * Network (can use `localhost` to talk)
  * Storage (shared volume)

---

### IP Allocation

* IP is **assigned to Pod**, not to individual containers.
* Use Pod's Cluster IP to access the container.

---

### What is `kubectl`?

* `kubectl` = CLI tool for Kubernetes
* Example commands:

  * `kubectl get nodes`
  * `kubectl get pods`
  * `kubectl delete pod <name>`

---

### Installing `kubectl` & `minikube`

1. Go to [Install kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) and select OS
2. Use curl to download binary (e.g., for macOS)
3. Confirm installation:

```bash
kubectl version --client
```

Install `minikube`:

1. Go to [Minikube Installation](https://minikube.sigs.k8s.io/docs/start/)
2. Select your OS and architecture (e.g., `arm64`, `x86_64`)
3. Use curl commands to download and install

---

### Starting a Local Kubernetes Cluster

```bash
minikube start
```

Optional (macOS):

```bash
minikube start --driver=hyperkit --memory=4096
```

This starts a **single-node cluster** using VM (like Docker Machine/HyperKit).

---

### Create Your First Pod:

1. Save YAML to `pod.yaml`
2. Apply the manifest:

```bash
kubectl create -f pod.yaml
```

3. Check pod status:

```bash
kubectl get pods
kubectl get pods -o wide
```

4. Check pod IP and access via curl:

```bash
minikube ssh
curl <POD-IP>
```

---

### Cheat Sheet for `kubectl`

Search Google: `kubectl cheat sheet`
Find all handy commands like:

* `kubectl logs <pod>`
* `kubectl describe pod <name>`

---

### Debugging Tools

```bash
kubectl logs <pod-name>
kubectl describe pod <pod-name>
```

---

### Clean-up

```bash
kubectl delete pod nginx
```

---

### What's Next?

To enable:

* **Auto healing**
* **Auto scaling**

You’ll move to using **Deployments**, which are wrappers over Pods with additional features like:

* Replica sets
* Rolling updates

Deployment YAML is similar to Pod YAML, but uses:

```yaml
kind: Deployment
```

You’ll learn this in the **next video**.

---

✅ Practice these commands and YAMLs.
Stay consistent to master core Kubernetes fundamentals before diving into deployments, services, and advanced configurations.

---

Would you like this full walkthrough as a downloadable PDF, or should I include it in your existing document?
