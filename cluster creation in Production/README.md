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
![image](https://github.com/user-attachments/assets/ff763822-6130-4832-b674-238a8e585a8a)
![image](https://github.com/user-attachments/assets/cc04a5bc-3e61-4bc8-9e4d-6f6542667d67)
![image](https://github.com/user-attachments/assets/e0c3879b-bd96-4939-85f1-3c81c7242634)
![image](https://github.com/user-attachments/assets/05e11344-df89-4561-b431-4a726a50f2db)

**Kubernetes in Production: Transcript (Formatted)**

---

going to talk about **Kubernetes Production Systems**. What I mean by that is: I'm going to explain how DevOps Engineers manage the **lifecycle** (creation, upgradation, configuration, and deletion) of Kubernetes clusters in production.

### Why is this important?

A lot of people practice Kubernetes on Minikube or other local Kubernetes setups like:

* K3s
* Kind
* K3d
* MicroK8s

These are good platforms to **learn and explore** Kubernetes, but remember: these are **development environments**, not suitable for production. Even Minikube’s official docs say it's for local use only.

### Real-world Kubernetes Use

In real-world scenarios, DevOps or Kubernetes administrators are responsible for creating infrastructure. If you're using Kubernetes in your organization, you're expected to manage its lifecycle.

So what Kubernetes do organizations use?
You should know about **Kubernetes distributions**.

---

### What is a Kubernetes Distribution?

Think of it like Linux. Linux is open-source, and people create **distributions** from it:

* Amazon Linux
* RedHat
* CentOS
* Ubuntu

These take the base Linux and wrap custom features/support around it.

Same with Kubernetes. It's an open-source container orchestration engine. Companies build distributions on top of it:

* Amazon: **EKS** (Elastic Kubernetes Service)
* RedHat: **OpenShift**
* VMware: **Tanzu**
* Rancher Labs: **Rancher**

These add support layers or enhance customer experience.

---

### Why Use a Distribution?

Distributions offer **support** and **security patches**. For example, with Amazon Linux or RedHat, you're paying, so they ensure your OS is secure and patched.
Similarly:

* If you face issues with EKS, Amazon supports you.
* If you manually install Kubernetes on EC2 and face issues, Amazon won’t help with Kubernetes—only EC2.

So distributions are popular because they offer paid support with SLAs.

---

### Common Distributions in Use (Popularity Order)

1. Kubernetes (open-source)
2. OpenShift
3. Rancher
4. VMware Tanzu
5. EKS (Amazon)
6. AKS (Azure)
7. GKE (Google)
8. Docker Kubernetes Engine

Do **not** say you're using Minikube/K3s in production during interviews.

---

### Why Use Kubernetes (open-source) in Production?

Some organizations use Kubernetes directly in production to save costs. E.g., if you have 100 dev teams with 10,000 developers, you can’t let everyone create their own EKS cluster—it will be very expensive.

So:

* Dev/staging: open-source Kubernetes
* Production: EKS or other distributions

Also, not every org needs <1-hour issue resolution, so open-source Kubernetes can be good enough.

---

### Minikube vs Real Kubernetes Setup

* **Minikube**: single node, low resource (2 CPUs, 4GB RAM)
* **Production Kubernetes**: multi-node, needs etcd storage, EBS volumes, network config

---

### Kubernetes vs EKS

* **Kubernetes on EC2**: you manage everything, no Amazon support
* **EKS**: managed by Amazon, you get support

EKS has wrappers like `eksctl`—just a more user-friendly version.

---

### How DevOps Engineers Manage Clusters in Production

One widely-used tool is **`kops`** (Kubernetes Operations).

Other tools:

* `kubeadm`
* `kubespray`

But `kops` is most used for:

* Creating
* Modifying
* Upgrading
* Deleting clusters

It manages the full Kubernetes lifecycle.

---

### OpenShift Setup (Alternate Option)

* Use RedHat VMs (not CentOS)
* Use **Ansible playbooks** provided by OpenShift docs
* Needs a RedHat subscription (not free-tier)

If you have AWS credits, you can try it. Otherwise, use `kops` for learning.

---

### Pre-requisites Before Installing with `kops`

* Create EC2
* Create EBS volumes (8GB+)
* Use Route 53 (if using a custom domain)

Costs may be incurred.

---

### GitHub Repo: `kubernetes-zero-to-hero`

* Contains all commands
* Follow instructions there

#### Dependencies:

* Python 3
* AWS CLI
* kubectl
* kops

You can do this setup on your local machine or a Ubuntu EC2 instance.

---

### Install AWS CLI and Configure:

```bash
aws configure
```

* Input AWS Access Key ID
* Input AWS Secret Access Key
* Set default region and output format

Go to AWS Console → Security Credentials → Create Access Keys

---

### IAM Permissions for Non-admin Users

If using an IAM user, assign these:

* EC2FullAccess
* S3FullAccess
* IAMFullAccess
* VPCFullAccess

If using an admin user, no extra config needed.

IAM Permissions for Non-admin Users

If using an IAM user, assign the following permissions:

EC2FullAccess

S3FullAccess

IAMFullAccess

VPCFullAccess

If using an admin user, no extra configuration is needed.

🔧 Steps to Set These IAM Permissions:

Log in to AWS ConsoleGo to https://console.aws.amazon.com/ and log in with an admin user.

Open IAM ServiceSearch for and open IAM in the AWS Management Console.

Choose or Create IAM User

Navigate to Users from the left sidebar.

If the user exists, click their username.

If not, click Add users and follow the prompts to create a new IAM user.

Attach Required Policies

Click Add permissions → Attach policies directly.

Search and select the following policies:

AmazonEC2FullAccess

AmazonS3FullAccess

IAMFullAccess

AmazonVPCFullAccess

Click Next and then Add permissions.

VerifyAfter saving, go to the Permissions tab of the IAM user and verify the 4 policies are listed.

(Optional) Create Access Key for CLI

Go to the Security credentials tab.

Click Create access key.

Use the key and secret in CLI setup via:
---

### Install `kops`

```bash
curl -LO https://github.com/kubernetes/kops/releases/latest/download/kops-linux-amd64
chmod +x kops-linux-amd64
sudo mv kops-linux-amd64 /usr/local/bin/kops
```

---

### Create S3 Bucket

```bash
aws s3api create-bucket \
  --bucket kops-ab-storage-1 \
  --region us-east-1
```

> Make the bucket name unique.

---

### Create Kubernetes Cluster

```bash
export KOPS_STATE_STORE=s3://kops-ab-storage-1

kops create cluster \
--name k8s.local \
--zones us-east-1a \
--node-count 2 \
--node-size t2.micro \
--master-size t2.micro \
--dns-zone k8s.local \
--state s3://kops-ab-storage-1 \
--yes
```

---

### Final Step: Start the Cluster

```bash
kops update cluster k8s.local --yes
```

This will take a few minutes. You’ll see: “Cluster configuration has been created.”

---

### Route 53 Domain Configuration (Optional)

```bash
aws route53 create-hosted-zone \
  --name dev.example.com \
  --caller-reference "unique-string"
```

Replace with your real domain.

> In interviews: say you used `.example.com` in prod, and `.k8s.local` in dev/staging.

---

### Cost-saving Tip

Do NOT experiment on kops-created clusters daily (they incur AWS charges).
Use **Minikube** for regular practice.

---

If you found this helpful, try it yourself (only if you have AWS credits), else stick to Minikube.


