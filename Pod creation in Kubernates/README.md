

🧠 **Day 33: Deploy Your First Application in Kubernetes**

---


The reason why I ask everyone to watch these videos is because from Docker to Kubernetes — before you start your journey with Kubernetes, you have to understand:

* The **differences between Docker and Kubernetes** — this is one part of it.
* The **architecture of Kubernetes**.
* **How to install Kubernetes**.



---

From Day 30, I have been stressing on a few points:

* **Why Kubernetes is better than Docker**, and
* **Why people move to Kubernetes**.

Some key reasons are:

1. Kubernetes is a **cluster**.
2. Kubernetes offers **auto scaling**.
3. Kubernetes offers **auto healing**.
4. Kubernetes offers **enterprise-level behavior**.

Using Kubernetes, you can support a lot of things for your containers. These are the four primary things.

To achieve all of these, you have to learn a few terminologies — just like we learned Docker terminologies in one of our previous classes.

---

### Introducing Key Kubernetes Concepts

I’m not going to talk about the architecture again because we already covered it. But I will introduce you to a few things to make your Kubernetes understanding better.

I don't want to jump directly into “what is a pod” or “how to deploy a pod” and run an application. I can do that in 15 minutes, but it won’t help if the fundamentals are not clear.

Firstly, we are moving from **Docker** to **Kubernetes** — from containers to container orchestration.

In Kubernetes, the **lowest level of deployment is a Pod**.

---

### Why Pod Instead of Container?

In Kubernetes, you cannot directly deploy a container like in Docker. In Docker, you build and deploy a container. In Kubernetes, we still use Docker containers internally, because end of the day both Docker and Kubernetes aim to run applications in containers.

But Kubernetes says: *Don't deploy your application as a container, deploy it as a **Pod**.*

So, what is a Pod?

A **Pod** is a definition of how to run a container. In Docker, when you run a container, you might use:

```bash
docker run -d -p 80:80 -v /data:/app nginx:1.14.2
```

You're passing various arguments like port mappings, volume mounts, and networks directly on the command line.

In **Kubernetes**, instead of the CLI command, you define those specifications in a `pod.yaml` file.

Kubernetes abstracts these container definitions in YAML, offering **standardization** and **declarative management**.

---

### YAML File Structure

Instead of using `docker run`, we define the same things in a structured YAML file:

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

You don’t need to memorize this. Everyone — junior or senior DevOps engineers — refers to the official documentation or templates.
In the command:

Here’s a **visual example** and a simple explanation of how `kubectl create -f pod.yaml` works.

---

### ✅ Sample `pod.yaml` File

```yaml
apiVersion: v1                # Version of the Kubernetes API
kind: Pod                     # Type of object being created
metadata:
  name: my-first-pod          # Name of the pod
spec:
  containers:
    - name: nginx-container   # Name of the container
      image: nginx            # Docker image to use
      ports:
        - containerPort: 80   # Port exposed by the container
```

---

### 💡 Explanation (line-by-line):

| Line                    | Explanation                                                   |
| ----------------------- | ------------------------------------------------------------- |
| `apiVersion: v1`        | Tells Kubernetes which version of the API you're using.       |
| `kind: Pod`             | Specifies you're creating a Pod object.                       |
| `metadata:`             | Metadata section to provide information about the object.     |
| `name: my-first-pod`    | The name you'll use to reference this pod.                    |
| `spec:`                 | Describes the actual content/configuration of the pod.        |
| `containers:`           | A list of containers inside the pod. You can run one or more. |
| `name: nginx-container` | Name of the container.                                        |
| `image: nginx`          | Docker image to run (`nginx` from Docker Hub).                |
| `ports:`                | Port(s) that the container will expose.                       |
| `containerPort: 80`     | The specific port on the container to expose.                 |

---

### ▶️ How to Use It

1. Save the above YAML in a file called `pod.yaml`.
2. Open your terminal and run:

```bash
kubectl create -f pod.yaml
```

3. To check if the pod was created:

```bash
kubectl get pods
```

---

### 📌 Visual Summary (Flow):

```text
Your Terminal
   |
   |--> kubectl create -f pod.yaml
         |
         |--> Reads pod.yaml
               |
               |--> Sends request to Kubernetes API Server
                     |
                     |--> Schedules pod on worker node
                           |
                           |--> kubelet + container runtime start the nginx container


In the command:

```bash
kubectl create -f pod.yaml
```

### `-f` stands for **file**

It tells `kubectl` to use a file that contains a Kubernetes object definition written in YAML or JSON format.

---

### Full Breakdown:

| Part       | Meaning                                                            |
| ---------- | ------------------------------------------------------------------ |
| `kubectl`  | The command-line tool to interact with your Kubernetes cluster     |
| `create`   | The operation you're performing — here, you're creating a resource |
| `-f`       | Short for `--filename`, used to specify the input file             |
| `pod.yaml` | The name of the YAML file that defines the Kubernetes resource     |

---

### Example:

If `pod.yaml` contains:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: nginx
      image: nginx
```

Then:

```bash
kubectl create -f pod.yaml
```

will create a pod named `my-pod` running the `nginx` container.

---

Let me know if you want a visual example or help creating a sample `pod.yaml` file!

```

Let me know if you'd like a diagram added to your Day 31 notes or a YAML file for download.

---

### Why YAML?

Kubernetes is an **enterprise-level platform**. It wants to:

* Enable **declarative capabilities**
* **Standardize** how resources are defined and managed

Everything in Kubernetes — whether it's a **pod**, **deployment**, or **service** — is written in **YAML**.

You don’t have to memorize the syntax. You need to understand **how** YAML works so you can modify templates.

---

### Single vs Multi-container Pods

Usually, a Pod contains a **single container**, but you can have **multiple containers** in one Pod when needed.

Example:

* A main application container
* A sidecar container that fetches config files

Benefits of multiple containers in a Pod:

* **Shared networking** (they communicate via `localhost`)
* **Shared storage** (via shared volumes)

This is useful in rare use cases and advanced scenarios like **init containers**, **sidecars**, and **service mesh**.

---

### How Kubernetes Handles IPs

* Pods are assigned a **Cluster IP address**.
* IPs are assigned to **Pods**, not individual containers.
* You access applications via the Pod’s IP.

---

A Pod is a **wrapper** around a container to simplify the life of DevOps engineers, especially when managing thousands of containers in production.

Instead of checking `docker run` arguments, a DevOps engineer can just open a `pod.yaml` and see everything:

* Which image is used
* Which port is exposed
* What volume is mounted
* What network settings are configured

... and so on.

---

✅ This is a properly formatted and clean version of your original transcript with zero word deletion. Would you like me to continue with the next portion in the same style?





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
**Kubernetes in Production: Transcript (Formatted Verbatim)**

---

Foreign, my name is Abhishek and welcome back to my channel.

Today we are at **Day 33** of our complete DevOps course, and in this class, we are going to see **how to deploy our first application in Kubernetes**.

Before watching this video, I'll highly recommend you to watch the previous videos (Day 30, 31, and 32).

The reason why I ask everyone to watch these videos is because from Docker to Kubernetes — before you start your journey with Kubernetes, you have to understand:

* The **differences between Docker and Kubernetes** — this is one part of it.
* The **architecture of Kubernetes**.
* **How to install Kubernetes**.

We covered these three topics in Day 30, 31, and 32. So, if you don't have the knowledge of these things, then I will recommend you to **not watch this video yet**. Go back, watch the videos, and then come back to this one. Only then you will understand today’s concept.

---

From Day 30, I have been stressing on a few points:

* **Why Kubernetes is better than Docker**, and
* **Why people move to Kubernetes**.

Some key reasons are:

1. Kubernetes is a **cluster**.
2. Kubernetes offers **auto scaling**.
3. Kubernetes offers **auto healing**.
4. Kubernetes offers **enterprise-level behavior**.

Using Kubernetes, you can support a lot of things for your containers. These are the four primary things.

To achieve all of these, you have to learn a few terminologies — just like we learned Docker terminologies in one of our previous classes.

---

### Introducing Key Kubernetes Concepts

I’m not going to talk about the architecture again because we already covered it. But I will introduce you to a few things to make your Kubernetes understanding better.

I don't want to jump directly into “what is a pod” or “how to deploy a pod” and run an application. I can do that in 15 minutes, but it won’t help if the fundamentals are not clear.

Firstly, we are moving from **Docker** to **Kubernetes** — from containers to container orchestration.

In Kubernetes, the **lowest level of deployment is a Pod**.

---

### Why Pod Instead of Container?

In Kubernetes, you cannot directly deploy a container like in Docker. In Docker, you build and deploy a container. In Kubernetes, we still use Docker containers internally, because end of the day both Docker and Kubernetes aim to run applications in containers.

But Kubernetes says: *Don't deploy your application as a container, deploy it as a **Pod**.*

So, what is a Pod?

A **Pod** is a definition of how to run a container. In Docker, when you run a container, you might use:

```bash
docker run -d -p 80:80 -v /data:/app nginx:1.14.2
```

You're passing various arguments like port mappings, volume mounts, and networks directly on the command line.

In **Kubernetes**, instead of the CLI command, you define those specifications in a `pod.yaml` file.

Kubernetes abstracts these container definitions in YAML, offering **standardization** and **declarative management**.

---

### YAML File Structure

Instead of using `docker run`, we define the same things in a structured YAML file:

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

You don’t need to memorize this. Everyone — junior or senior DevOps engineers — refers to the official documentation or templates.

---

### Why YAML?

Kubernetes is an **enterprise-level platform**. It wants to:

* Enable **declarative capabilities**
* **Standardize** how resources are defined and managed

Everything in Kubernetes — whether it's a **pod**, **deployment**, or **service** — is written in **YAML**.

You don’t have to memorize the syntax. You need to understand **how** YAML works so you can modify templates.

---

### Single vs Multi-container Pods

Usually, a Pod contains a **single container**, but you can have **multiple containers** in one Pod when needed.

Example:

* A main application container
* A sidecar container that fetches config files

Benefits of multiple containers in a Pod:

* **Shared networking** (they communicate via `localhost`)
* **Shared storage** (via shared volumes)

This is useful in rare use cases and advanced scenarios like **init containers**, **sidecars**, and **service mesh**.

---

### How Kubernetes Handles IPs

* Pods are assigned a **Cluster IP address**.
* IPs are assigned to **Pods**, not individual containers.
* You access applications via the Pod’s IP.

---

A Pod is a **wrapper** around a container to simplify the life of DevOps engineers, especially when managing thousands of containers in production.

Instead of checking `docker run` arguments, a DevOps engineer can just open a `pod.yaml` and see everything:

* Which image is used
* Which port is exposed
* What volume is mounted
* What network settings are configured

... and so on.

✅ This is a properly formatted and clean version of your original transcript with zero word deletion.

### What is `kubectl`?

So, what is kubectl? Just like in Docker you use the Docker CLI to run containers, in Kubernetes you use the `kubectl` CLI tool to interact with your Kubernetes clusters.

You can use commands like:

* `kubectl get nodes` — to list all nodes in the cluster
* `kubectl get pods` — to list all running pods
* `kubectl delete pod <name>` — to delete a specific pod

---

### Installing `kubectl` and Minikube

#### Step 1: Install kubectl

* Go to Google and search "kubectl installation"
* Click on the Kubernetes page: `install tools - Kubernetes`
* Choose your OS (Linux, macOS, Windows)
* For macOS (with Silicon chip e.g., M1/M2):

  * Copy the script
  * Run it in terminal
* Confirm installation:

```bash
kubectl version --client
```

#### Step 2: Install Minikube

* Search for "Minikube install"
* Visit the Minikube Kubernetes page
* Select your operating system
* Choose architecture: `x86_64` or `arm64` based on your system
* Follow installation steps (copy the curl script and install)

Verify Minikube:

```bash
minikube version
```

---

### Creating Your First Kubernetes Cluster

#### Start Minikube:

```bash
minikube start
```

Optionally, specify resources:

```bash
minikube start --driver=hyperkit --memory=4096
```

> Minikube creates a **single-node Kubernetes cluster** on a local VM (e.g., via Docker, VirtualBox, or Hyperkit).

#### Check Node Status:

```bash
kubectl get nodes
```

You’ll see your Minikube node with status `Ready`.

This node acts as both the **Control Plane** and **Worker Node**.

---

### Deploying Your First Pod

Search "Kubernetes pod yaml example" and copy this default sample:

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

> Save it as `pod.yaml`

Run the pod:

```bash
kubectl create -f pod.yaml
```

Verify pod status:

```bash
kubectl get pods
kubectl get pods -o wide
```

Check IP address and test with curl (within the cluster):

```bash
minikube ssh
curl <pod-ip>
```
minikube ssh: Used to log in to the Minikube VM so you can access the pod’s internal network.

curl <pod-ip>: Run from within the Minikube VM to test connectivity to the pod IP, since it’s only accessible internally.
---

### Clean Up Pod

```bash
kubectl delete pod nginx
```

---

### Debugging Pods in Kubernetes

You can debug your application using:

```bash
kubectl logs <pod-name>
```

This command shows logs from inside the pod's container.

Another useful command:

```bash
kubectl describe pod <pod-name>
```

This gives a full status report:

* Events
* Resource allocations
* Errors (if any)

For example:

```bash
kubectl logs nginx
kubectl describe pod nginx
```

> `logs` helps you read runtime output.
> `describe` helps you understand setup issues, state, events, and container health.

If your application doesn’t produce logs by default (like nginx), there might not be much to see in `kubectl logs`. But for real apps, this is very useful.

---

### What's Next?

We deployed our first pod, accessed it, and debugged it.

Next step: enable auto-scaling and auto-healing. These features come from using **Deployments**, not just Pods.

Pods are the basic units, but:

* **Deployments** wrap Pods
* They enable replication, rollouts, rollbacks, auto-healing, and scaling

In the next session, we will:

* Write a `deployment.yaml`
* Learn how Deployments relate to Pods
* Understand how to make our application production-ready

So today’s video is fundamental. Practice everything.

👉 Also revisit the earlier videos on:

* Docker basics
* Kubernetes installation
* Cluster structure


Debugging Pods in Kubernetes

Command Breakdown and Syntax Explanation

1. minikube ssh

minikube: CLI tool to manage the local Kubernetes cluster.

ssh: Command to log into the virtual machine that Minikube is running.

📝 Purpose: Used when you want to access the internal environment of the Kubernetes VM created by Minikube. This lets you run commands inside the cluster (e.g., using curl to test a Pod's IP).

2. curl <pod-ip>

curl: A CLI tool to make HTTP requests.

: Replace this with the actual IP address of the Pod (seen from kubectl get pods -o wide).

📝 Purpose: Used to send an HTTP request to the Pod's internal IP to verify if the application (e.g., nginx) is responding.

Important: This command works only inside the cluster, so use it after running minikube ssh.

3. kubectl get pods

kubectl: Kubernetes CLI tool.

get: Fetch resources.

pods: Resource type — shows a list of all Pods in the current namespace.

4. kubectl get pods -o wide

-o wide: Extended output including Pod IPs, nodes, and more.

5. kubectl logs <pod-name>

logs: Retrieves logs generated by the container inside the Pod.

: Name of your running Pod.

6. kubectl describe pod <pod-name>

describe: Gives a detailed report about the Pod (status, events, reasons for failures).

7. kubectl delete pod <pod-name>

delete: Removes the resource.

pod: The type of resource to delete.

: The name of the Pod you want to delete.

✅ These commands are the primary building blocks of Kubernetes troubleshooting and management. Practice them to build fluency in managing cluster resources.

You can debug your application using:

kubectl logs <pod-name>

This command shows logs from inside the pod's container.

Another useful command:

kubectl describe pod <pod-name>

This gives a full status report:

Events

Resource allocations

Errors (if any)

For example:

kubectl logs nginx
kubectl describe pod nginx

logs helps you read runtime output.
describe helps you understand setup issues, state, events, and container health.

If your application doesn’t produce logs by default (like nginx), there might not be much to see in kubectl logs. But for real apps, this is very useful.

