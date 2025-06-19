# Day 40 - Kubernetes Custom Resources Deep Dive

Welcome to **Day 40** of our Complete DevOps Course. In this session, we explore one of the advanced topics in Kubernetes: **Custom Resources**. This documentation retains the original spoken transcript exactly and formats it into a professional, readable layout.

---

## 📢 Announcement

Before we start the session:

> *"If you haven't subscribed to my channel, please do. I’ll soon be announcing my future roadmap: upcoming masterclasses, free courses, and more! Stay tuned!"*

---

## 🎯 Today's Agenda

We will understand:

* What is a **Custom Resource Definition (CRD)**
* What is a **Custom Resource (CR)**
* What is a **Custom Controller**

---

## 🚀 Kubernetes Native Resources Overview

By default, Kubernetes comes with built-in resources like:

* Deployment
* Service
* Pod
* ConfigMap
* Secret

These are called **native Kubernetes resources**. They are supported by default and managed by Kubernetes controllers.

But what if Kubernetes doesn’t support your use case (e.g., advanced security or GitOps)? That’s where **custom resources** come into play.

---

## 💡 Why Extend Kubernetes?

To add functionality not natively supported by Kubernetes, you can:

* Use external tools like **Kyverno**, **Kube-hunter**, **ArgoCD**, **Istio**, etc.
* Introduce your own **Custom Resource Definitions (CRDs)**

The **CNCF landscape** is full of tools that implement CRDs and custom controllers to bring advanced features to Kubernetes clusters.

---

## 🎭 Actors in the Custom Resource Flow

* **DevOps Engineer**:

  * Deploys the CRD
  * Deploys the Custom Controller
* **User/Developer**:

  * Deploys the Custom Resource (CR)

---

## 🧩 The 3 Key Components

1. **CRD (Custom Resource Definition)**
2. **CR (Custom Resource)**
3. **Custom Controller**

These three components work together to extend the Kubernetes API.

---

## 📜 Understanding Custom Resource Definitions (CRD)

* A **CRD** defines a new API type (like a deployment but custom).
* It tells Kubernetes what fields are valid in a custom resource.
* Example: `istio.io` might define a CRD for `VirtualService`.

Like how Kubernetes knows what fields are valid in `deployment.yaml`, it will validate custom resources using the CRD.

---

## 📦 Understanding Custom Resources (CR)

* A **CR** is what users create based on the CRD.
* Example: A user might create a `VirtualService` resource using the Istio CRD.

```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: my-app
spec:
  ...
```

---

## ⚙️ Understanding Custom Controllers

* A **custom controller** is a program (usually in Go) that watches for changes to a custom resource (CR) and performs actions.
* It acts like the native Kubernetes `DeploymentController` but for your own custom logic.
* It reads the CR and performs the necessary action.

---

## 🔁 Full Flow Summary with Example (Istio)

1. DevOps deploys the CRD (`VirtualService CRD`) to Kubernetes
2. User creates a CR (`VirtualService`) in namespace `abhishek`
3. Kubernetes validates the CR against the CRD
4. Nothing happens until...
5. DevOps deploys the **Istio custom controller**
6. The controller reads the CR and enables service mesh features

---

## 🛠️ How to Write a Custom Controller

* Usually written in **Go** using `client-go`
* Uses:

  * Watchers
  * Reflectors
  * Worker queues
* You can also use `controller-runtime` or `operator-sdk`

> *“Initially we had to write everything from scratch in 2015, now frameworks help simplify a lot of this!”*

---

## 🧪 Practice Example: Istio CRD + Controller

### 1. Go to Istio GitHub repo

[https://github.com/istio/istio](https://github.com/istio/istio)

### 2. Explore CRDs and Controllers

```bash
kubectl get crd | grep istio
```

### 3. Deploy using Helm

```bash
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm repo update
kubectl create ns istio-system
helm install istio-base istio/base -n istio-system
```

---

## 🧠 Recap

To extend Kubernetes:

1. Deploy the **CRD** (defines a new resource type)
2. Deploy the **Custom Controller** (logic to act on CR)
3. Users create **CRs** (use the new feature)

---

## 💬 Final Note

As a DevOps engineer:

* You don’t need to write custom controllers unless required
* But you should know how to **deploy** and **debug** them
* Understand how to work with popular tools like Istio, ArgoCD, Prometheus, etc.

---

## ✅ Next Steps

* Go through Istio documentation: [https://istio.io](https://istio.io)
* Try creating and deploying a CRD, CR, and Controller
* Subscribe to stay updated for masterclasses and deep dives

> *“Thank you so much for watching the video today. I’ll see you in the next video. Take care everyone, bye!”*

---
