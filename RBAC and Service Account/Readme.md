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
## Kubernetes RBAC (Role-Based Access Control) - Concept and Live Demo

**Speaker: Abhishek**

---

Hello everyone, welcome back to my channel. In this video, I'll be talking about **Kubernetes RBAC**.

This is not just going to be a theoretical video. I'll also show you a **live demo** at the end of this video, so please watch it till the end. This way, you will not only understand the concept of RBAC, but also know how to apply it on a live cluster.

---

### What is RBAC?

**RBAC** or **Role-Based Access Control** is a method of **regulating access** to computer or network resources. This is a quite generic definition.

Let’s understand this in terms of **Kubernetes**.

If you already know what a **Service Account** is in Kubernetes, then imagine this:

* You have a service account in your cluster.
* You want to grant that service account a set of **permissions**.

  * Example: It can **list pods** in a specific namespace.
  * Or it can **perform operations** in a namespace or even manage the entire Kubernetes cluster.

To achieve this:

* You need to **assign the service account a Role** corresponding to that permission.
* Then, use a **RoleBinding** to bind the role to the service account.

So, there are 3 core components:

* **Role**
* **RoleBinding**
* **ServiceAccount**

**RoleBinding** takes care of attaching a role to a service account.

---

### Simple Example

Let’s say:

* The service account only needs permission to **get**, **list**, and **watch** pods in a namespace (e.g., `default`).
* You create a **Role** with those permissions.
* Then, you create a **RoleBinding** to bind the service account to the role.

We will now see this with a **live demo**.

---

## Demo Time

### Step 1: Cluster Setup

* A **Kubernetes cluster** is already created (a clean cluster).

### Step 2: Create Namespace

```bash
kubectl create namespace test
```

### Step 3: Create Service Account

```yaml
# service.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: foo
  namespace: test
```

```bash
kubectl apply -f service.yaml
```

Now a service account called **foo** exists in the `test` namespace.

### Step 4: Check Service Account Permissions

```bash
kubectl auth can-i get pods \
  --as=system:serviceaccount:test:foo \
  --namespace=test
```

**Answer**: No (because no permissions assigned yet).

---

### Step 5: Create Role

```yaml
# role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: test
  name: test-admin
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["*"]
```

```bash
kubectl apply -f role.yaml
```

Check permissions again – still **No** (because not yet bound).

---

### Step 6: Create RoleBinding

```yaml
# rolebinding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: test-admin-binding
  namespace: test
subjects:
- kind: ServiceAccount
  name: foo
  namespace: test
roleRef:
  kind: Role
  name: test-admin
  apiGroup: rbac.authorization.k8s.io
```

```bash
kubectl apply -f rolebinding.yaml
```

### Step 7: Check Again

```bash
kubectl auth can-i get pods \
  --as=system:serviceaccount:test:foo \
  --namespace=test
```

**Answer**: Yes

Now the service account **foo** can:

* Get pods
* Create pods
* Deploy workloads

---

### Test Other Namespaces

```bash
kubectl auth can-i get pods \
  --as=system:serviceaccount:test:foo \
  --namespace=kube-system
```

**Answer**: No (because it’s a namespaced role, not cluster-wide).

---

## ClusterRole and ClusterRoleBinding

Let’s test with cluster-wide permissions.

```yaml
# clusterrolebinding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: foo-cluster-admin-binding
subjects:
- kind: ServiceAccount
  name: foo
  namespace: test
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

```bash
kubectl apply -f clusterrolebinding.yaml
```

Now test again:

```bash
kubectl auth can-i create deployments \
  --as=system:serviceaccount:test:foo \
  --namespace=kube-system
```

**Answer**: Yes

Test another namespace:

```bash
kubectl auth can-i delete pods \
  --as=system:serviceaccount:test:foo \
  --namespace=default
```

**Answer**: Yes

### Why?

Because you bound the service account with a **ClusterRoleBinding** using **cluster-admin**, it now has full permissions across the cluster.

---

## Summary

* Role: namespace-scoped
* ClusterRole: cluster-wide scope
* RoleBinding: binds Role to user/service account in a namespace
* ClusterRoleBinding: binds ClusterRole to user/service account across cluster

---

### Closing Remarks

That’s the video, guys. If you have any questions, please post them in the comment section. I definitely reply to all of them.

If you want me to do a specific video on any concept, please let me know.

Thank you! Let’s meet in the next video.
