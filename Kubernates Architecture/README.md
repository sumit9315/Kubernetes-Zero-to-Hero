**Kubernetes in Production: Transcript – Day 31 (Grammar Corrected, Full Transcript with Original Wording)**

---

Hello everyone, my name is Abhishek and welcome back to my channel. So today we are at Day 31 of our free DevOps course, and in this video, I'll be talking about the Kubernetes architecture.

So before we jump onto the topic for today, let me start with a very lighter note question: Why is Kubernetes called K8s? So everybody knows that Kubernetes in short is called K8s, but to just start with a fun question, let's see how many people can answer this: why is Kubernetes actually called K8s? This is not at all an interview question. I'm just trying to start with a simple question because we are going to deal with a very complicated concept.

**Docker vs Kubernetes**

So coming to the architecture of Kubernetes, firstly you should understand the difference between Docker and Kubernetes. That is the same thing we tried to understand on Day 30. So if you haven't watched our previous video (Day 30), I highly recommend you watch that before watching this video on the architecture of Kubernetes.

The reason I'm telling you this is if you don't understand what a Docker platform or what a container platform offers, and the reason why we need to evolve to a container orchestration platform, you will never understand the reason for Kubernetes architecture.

On a very high level, what I told you is Kubernetes offers four fundamental advantages over Docker:

1. Kubernetes is by default cluster in nature (cluster behavior).
2. Kubernetes offers auto-healing.
3. Kubernetes offers auto-scaling.
4. Kubernetes offers multiple enterprise-level supports (advanced load balancing, security features, advanced networking, etc.)

So we understood these four things in detail, and today I am going to explain the architecture of Kubernetes also using these four examples.

You might ask: “Abhishek, there are hundreds of videos on the internet which explain Kubernetes architecture.” Right. Everybody says Kubernetes has something called a control plane and something called a data plane. So this is something everybody explains, and probably if you've watched the previous videos or read the Kubernetes documentation, you know that the control plane has components like:

* API Server
* etcd
* Scheduler
* Controller Manager
* Cloud Controller Manager

Similarly, in the data plane you have components like:

* Kubelet
* Kube-proxy
* Container Runtime

But what exactly are all of these? Even I can just list these components and what each does, but you will never understand Kubernetes architecture this way. That’s why I will compare this with Docker.

Let’s understand two basic things:

* In Docker, the simplest thing is a **container**.
* In Kubernetes, the simplest thing is a **pod**.

I'll compare both and show how a container is created in Docker and how a pod is created in Kubernetes. This way you’ll understand the architecture of Kubernetes, and why it needs these many components.

Let’s start with **Docker**:
Assume you have a virtual machine with Docker installed. You’ve written a Dockerfile and built images. Then, you run a container with a basic command like:

```sh
Docker run
```

But what’s happening under the hood?
Even if you run a container, the application (e.g., Java app) needs the proper runtime. If Java runtime isn't installed, it won’t work. Similarly, even for containers, you need something called a **container runtime**.

In Docker, that container runtime is called **Docker shim**.

Now, let’s move to **Kubernetes**:
Kubernetes does something similar but with more advanced capabilities. It supports enterprise-level features, so it creates a master and a worker. For simplicity, let’s assume a 1 master + 1 worker node architecture.

In production, there are multiple masters and workers, but for now, we’ll keep it simple.

In Kubernetes, requests don’t directly go to workers. They go through the **control plane** (master). So when you deploy a pod (the smallest deployment unit in Kubernetes), it’s like deploying a container.

A **pod** is like a wrapper over a container with advanced capabilities.

Your pod gets deployed on a worker node. The **kubelet** on the worker node is responsible for running your pod. Think of kubelet like Docker Engine. It maintains the pod and reports its status.

Even in Kubernetes, a container inside a pod needs a **container runtime**. The difference here is:

* Docker uses Docker shim only.
* Kubernetes can use:

  * Docker shim
  * containerd
  * CRI-O
  * Any runtime implementing Kubernetes Container Interface (KCI).

So now we’ve discussed:

1. **kubelet** – maintains and ensures pod health.
2. **container runtime** – runs the container inside the pod.

In Docker, you also have **bridge networking** like Docker0.
In Kubernetes, that is handled by **kube-proxy**. It provides:

* Networking (allocates IPs)
* Load balancing (for auto-scaled replicas)
* Uses `iptables` on Linux to manage routing

So on the **worker node**, you have 3 components:

* kubelet
* kube-proxy
* container runtime

These three make up the **data plane**.

**Control Plane (Master)**

The worker node is sufficient to run applications, but the control plane manages them intelligently.

Why is the control plane needed?

* For deciding which pod should go to which node (Node1 or Node2)
* For handling multiple users, scaling, security, etc.

### Components of Control Plane:

1. **API Server**:

   * Heart of Kubernetes
   * Exposes Kubernetes to external world
   * Takes incoming requests (like create pod, deploy app)

2. **Scheduler**:

   * Schedules pods to appropriate nodes (based on resource availability)

3. **etcd**:

   * Cluster store (key-value store)
   * Stores all cluster state info

4. **Controller Manager**:

   * Manages built-in controllers (e.g., replica set)
   * Ensures desired state matches actual state

5. **Cloud Controller Manager**:

   * Needed for Kubernetes on cloud providers (EKS, AKS, GKE)
   * Handles cloud-specific logic like provisioning LoadBalancers or storage
   * Can be customized for new cloud providers

If you’re running Kubernetes **on-prem**, the Cloud Controller Manager is not required.

### Summary Diagram (Verbally Explained)

If you were to draw this:

* Control Plane:

  * API Server
  * Scheduler
  * etcd
  * Controller Manager
  * Cloud Controller Manager

* Data Plane (Each Worker Node):

  * kubelet
  * kube-proxy
  * container runtime

Control plane **controls** the actions. Data plane **executes** them.

### Assignment:

Draw a diagram showing control plane and data plane, showing how components interact.

* Use pod creation as an example.
* Post the notes and diagram on LinkedIn/GitHub.

This helps your visibility and shows your understanding to potential interviewers.

If you have questions, drop them in the comments. I’ll reply and explain further.

If you liked the video, click like, share with friends/colleagues, and subscribe.

Thanks for watching, and I’ll see you in the next video where we’ll learn about Kubernetes pods.
