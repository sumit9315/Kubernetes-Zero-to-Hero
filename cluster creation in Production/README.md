**Kubernetes in Production - Complete Notes with Commands and Explanations**

---

## 1. **Overview of Kubernetes in Production**

### Why it's important:

* Local Kubernetes tools like **Minikube**, **K3s**, **Kind**, etc., are **for learning** or **development only**.
* Real production setups need full-fledged, **high availability** configurations.
* DevOps/Cloud Engineers are expected to manage the **full lifecycle**: creation, configuration, upgrades, and deletion of Kubernetes clusters.

---

## 2. **What is a Kubernetes Distribution?**

> Like Linux has distributions (Ubuntu, CentOS, RedHat), Kubernetes also has distributions.

### Examples:

* **Open Source Kubernetes** (vanilla)
* **Amazon EKS** (Managed by AWS)
* **RedHat OpenShift**
* **VMware Tanzu**
* **Rancher by Rancher Labs**

### Key Differences:

| Vanilla Kubernetes              | Managed Kubernetes (EKS, OpenShift, etc.) |
| ------------------------------- | ----------------------------------------- |
| You manage and install manually | Provider manages the setup, upgrades      |
| No official support             | Paid support available                    |
| Customizable                    | Opinionated but stable                    |

---

## 3. **Popular Kubernetes Distributions (Used in Production)**

1. **Kubernetes (Open Source)**
2. **OpenShift (Red Hat)**
3. **Rancher (SUSE)**
4. **VMware Tanzu**
5. **Amazon EKS**
6. **Azure AKS**
7. **Google GKE**
8. **Docker Kubernetes Engine**

> Note: **Do not say you're using Minikube or K3s in interviews for production work**.

---

## 4. **Kubernetes vs EKS**

| Kubernetes Installed Manually               | Amazon EKS (Managed)               |
| ------------------------------------------- | ---------------------------------- |
| You are responsible for setup & maintenance | AWS handles provisioning, upgrades |
| No support from AWS if issues               | AWS offers support & SLAs          |
| More customization                          | Less flexibility                   |

---

## 5. \*\*Tool to Install Kubernetes in Production: \*\***`kops`**

### Why `kops`?

* Stands for **Kubernetes Operations**
* Supports **cluster lifecycle management**:

  * Create
  * Upgrade
  * Modify
  * Delete

### Alternatives:

* `kubeadm`
* `Rancher`
* `OpenShift` (Ansible-based)

---

## 6. **Pre-requisites for ****`kops`**** Setup**

### Tools to Install:

* `Python3`
* `AWS CLI`
* `kubectl`
* `kops`

### IAM Permissions Required:

If NOT using root AWS account:

* `EC2FullAccess`
* `S3FullAccess`
* `IAMFullAccess`
* `VPCFullAccess`

---

## 7. **Commands and Setup Process**

### Step 1: Install AWS CLI & Configure

```bash
aws configure
```

* Input:

  * AWS Access Key ID
  * AWS Secret Access Key
  * Default region (e.g. `us-east-1`)
  * Output format (e.g. `json`)

### Step 2: Install `kops`

```bash
curl -LO https://github.com/kubernetes/kops/releases/latest/download/kops-linux-amd64
chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops
```

### Step 3: Create an S3 Bucket for Cluster Config Storage

```bash
aws s3api create-bucket \
  --bucket kops-abhishek-storage-1 \
  --region us-east-1
```

> Replace bucket name with something unique.

### Step 4: Export Environment Variable for Kops

```bash
export KOPS_STATE_STORE=s3://kops-abhishek-storage-1
```

### Step 5: Create Kubernetes Cluster

```bash
kops create cluster \
--name k8s.local \
--zones us-east-1a \
--node-count 2 \
--node-size t2.micro \
--master-size t2.micro \
--dns-zone k8s.local \
--state s3://kops-abhishek-storage-1 \
--yes
```

> For real domains (e.g., xyz.com), configure in Route 53.

### Step 6: Apply Configuration and Start Cluster

```bash
kops update cluster k8s.local --yes
```

> Wait a few minutes for the cluster to start.

---

## 8. **Route 53 (Optional if using real domain)**

### Create Hosted Zone (if using real domain):

```bash
aws route53 create-hosted-zone \
  --name dev.example.com \
  --caller-reference "unique-string"
```

---

## 9. **Cost Caution and Local Learning**

* Creating real clusters via `kops` will use:

  * EC2
  * EBS
  * S3
  * Route 53 (if domain used)
* This incurs cost. Use **Minikube** or **Kind** for local practice.

---

## 10. **Interview Tips**

* NEVER say you're using Minikube for production.
* Always mention a valid distribution:

  * "We use EKS for production, and manage staging with open-source Kubernetes."
  * "I have experience installing Kubernetes clusters using `kops` on AWS."
* Use `.k8s.local` for dev/testing, `.example.com` for production.

---

## 11. **Useful GitHub Repo Mentioned**

GitHub: [kubernetes-zero-to-hero](https://github.com)

* Contains step-by-step commands.
* Star this repo for future Kubernetes setups.

---

## 12. **Next Step**

* Use this setup as a learning base.
* Practice on Minikube to avoid costs.
* Be confident to discuss:

  * Cluster lifecycle
  * Distribution choices
  * Setup tools like `kops`
  * IAM + AWS CLI

---

End of Notes ✅
