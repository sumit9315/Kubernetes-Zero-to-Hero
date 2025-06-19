## Kubernetes RBAC and OpenShift Sandbox (Day 39 - Complete DevOps Course)

**Speaker: Abhishek**

---

Hello everyone, my name is Abhishek and welcome back to my channel. Today is **Day 39** of our complete DevOps course. In this class, we'll be talking about **Kubernetes RBAC**. As I promised, and as you might have seen in the thumbnail, I am going to show you how to create a **30 days free OpenShift cluster**.

### Free OpenShift Cluster

This cluster will be **free for 30 days**, and you can create and manage resources in it to play around and learn OpenShift concepts. We'll talk about that at the end of the video. For now, let’s focus on **Kubernetes RBAC**.

---

### What is Kubernetes RBAC?

RBAC stands for **Role-Based Access Control**. It is a simple yet powerful concept.

#### Why is it important?

RBAC is directly related to **security**. If it is not implemented correctly, it can:

* Be **complicated to debug**
* Cause **critical security issues**

Understanding the **concept** is more important than just knowing how to create a role, service account, or role binding.

---

### Two Categories of Access in Kubernetes RBAC

1. **User Management**
2. **Service Account (application/service-level access)**

#### 1. User Management

* On local tools like Minikube or KIND, you have **admin access**.
* In a real organization, you need to define who (e.g., Devs, QEs) has what access.
* **RBAC defines roles** and what each role can do.
* For example:

  * A developer shouldn't be able to delete resources in the kube-system namespace.

#### 2. Service Accounts

* When a **Pod** is created (e.g., via Deployment), what access does it have?
* Can it access **Secrets**, **ConfigMaps**, etc.?
* Even malicious pods can create damage if not restricted.

Both user and service-level access are managed using **RBAC**.

---

### RBAC Core Concepts

There are **three key components**:

1. **Users / Service Accounts**
2. **Roles / Cluster Roles**
3. **Role Bindings / Cluster Role Bindings**

Let’s break them down.

---

### Creating Users in Kubernetes

* In **Linux**, you can create users via `useradd`, but **Kubernetes does not manage users**.
* Instead, it **offloads user management to Identity Providers (IdPs)**.
* Example IdPs:

  * Google, GitHub (Login with Google/Facebook etc.)
  * AWS IAM
  * Azure AD
  * LDAP, OKTA, Keycloak, etc.

#### Real-World Example:

* On **EKS (AWS)**: you can use IAM users to login to the Kubernetes cluster.
* On **GitHub**: use OAuth via Keycloak to authenticate.

---

### Kubernetes API Server and OAuth

* Kubernetes API Server acts as the **OAuth server**.
* Pass certain flags to configure OAuth authentication.
* You can configure any external Identity Provider for user authentication.

#### Service Account

* Created easily via YAML (e.g., `sa.yaml`).
* Every pod gets a **default service account** if not explicitly assigned.
* This is used to communicate with the **API Server**.

---

### Roles and Role Bindings

Once you have a user or service account:

#### Role

* A YAML defining what actions are allowed on what resources.
* Used within a **namespace**.
* Use **ClusterRole** for cluster-wide access.

#### RoleBinding

* Binds a **Role to a user/service account**.
* Use **ClusterRoleBinding** for cluster-wide access.

#### Visual Summary:

```
User/ServiceAccount --> Role --> RoleBinding
```

* Role = defines permissions
* RoleBinding = attaches role to user/service account

#### Analogy:

Roles are like IAM policies in AWS.

---

### Recap of Kubernetes RBAC Flow

* Create a **Service Account** or user.
* Create a **Role** or ClusterRole.
* Use **RoleBinding** or ClusterRoleBinding to bind them.

If you're a beginner, don’t worry about the difference between role and cluster role for now. We'll cover this during practicals.

---

## Creating a Free OpenShift Sandbox (30-Day Trial)

### Steps:

1. Open **Firefox (or any browser)** in incognito.
2. Search for **OpenShift Sandbox**.
3. Click **Start Your Sandbox for Free**.
4. Sign up for a **Red Hat account**.

   * If you already have one, just log in.
5. Once logged in, click **Start Using Sandbox**.
6. You get a **shared OpenShift cluster** for 30 days.

### Identity Provider in OpenShift

* OpenShift uses the **Dev Sandbox** identity provider.
* This handles login, authorization, and user info.

### Once Logged In

* You'll get access to a namespace dedicated to you.
* Limited access (since it's shared).
* Can view:

  * **Workloads (Pods, Deployments)**
  * **Services, Routes, Events**

### Copy Login Command

* Use the UI to copy the **login token**.
* Paste into your terminal to login via CLI:

```sh
oc login --token=... --server=https://...
```

* Now you can run commands like:

```sh
kubectl get pods
kubectl create deployment nginx --image=nginx
```

### Monitoring

* See deployments via UI.
* Scale pods up/down.
* Create Ingress, persistent volumes, services.
* View events in UI (e.g., image pull, pod start).

---

### What to Do Next?

* In the **next class**, we will:

  * Create **service accounts**
  * Create **roles**
  * Create **role bindings**

---

### Final Words

* Try creating your OpenShift account before the next video.
* Real-time practice boosts confidence.
* Helps in cracking interviews.

If you have any questions, comment below. Don’t forget to **subscribe to my channel** - Abhishek.

**Thank you! See you in the next video.**

---
