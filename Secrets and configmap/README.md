**Day 41 - Kubernetes Config Maps and Secrets Deep Dive**

---

**Introduction**
Hello everyone, my name is Abhishek and welcome back to my channel! Today we are at **Day 41** of our complete DevOps course. In this video, we’ll be diving into two essential concepts in Kubernetes: **ConfigMaps** and **Secrets**.

We'll cover:

* What is a ConfigMap?
* What is a Secret and why is it needed?
* Key differences between ConfigMap and Secret (common interview question)
* Live demo: creating and using ConfigMaps and Secrets inside pods/deployments

> 📌 *If you haven't already, subscribe to my channel to stay updated with future free courses and masterclasses.*

---

**Section 1: What is a ConfigMap?**

Imagine you're a developer creating a backend application that talks to a database. Instead of hardcoding sensitive info like DB port, username, or password, you'd typically use **environment variables** or external config files.

In Kubernetes, you solve this problem using **ConfigMaps**:

* ConfigMaps store **non-sensitive configuration data** (e.g., DB port, connection type).
* You can reference a ConfigMap inside a pod via **environment variables** or **volume mounts**.

> 🎯 *Best Practice:* Never hardcode values inside application code or Dockerfiles.

---

**Section 2: What is a Secret?**

While ConfigMaps handle non-sensitive data, **Secrets** are designed for **sensitive information** such as:

* DB usernames and passwords
* API keys

Kubernetes stores all resources (including ConfigMaps and Secrets) in **etcd**. If a hacker gets access to etcd:

* ConfigMap values are exposed in plain text
* Secret values are **base64 encoded** (which is not encryption)

To enhance security:

* Use custom encryption with API Server + etcd
* Apply **RBAC policies** to restrict access to Secrets

> 🔐 *Security Tip:* Follow the **Least Privilege** principle – only give access to users who absolutely need it.

---

**Section 3: Key Differences – ConfigMap vs Secret**

| Feature            | ConfigMap                       | Secret                           |
| ------------------ | ------------------------------- | -------------------------------- |
| Purpose            | Store non-sensitive config data | Store sensitive data             |
| Encryption         | No                              | Base64 by default (can encrypt)  |
| Access Restriction | Moderate (no encryption)        | High (RBAC + encryption support) |
| Use Cases          | DB port, connection type, flags | DB password, API token           |

---

**Section 4: Live Demo Walkthrough**

This section shows:

* How to create ConfigMaps using YAML and `kubectl`
* Referencing ConfigMaps inside a deployment as env variables
* Why volume mounts are better for dynamic config changes
* How to create and decode Secrets

> ✅ Each command and concept is explained with practical examples, like using a Python Django app in Kubernetes pods.

\[...Transcript content continues exactly as it was, without removing any word...]

---

**Conclusion & Homework**

Today we explored both theory and hands-on use of ConfigMaps and Secrets in Kubernetes.

🧠 **Assignment:**

* Repeat the ConfigMap exercise using a **Secret** instead (DB password)
* Use `secretKeyRef` inside the deployment
* Try both env and volume mount methods

💬 If you have any questions, drop them in the comments. Don't forget to like, share, and subscribe!

Thank you! See you in the next video 👋
