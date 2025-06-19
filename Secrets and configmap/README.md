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

This section is a detailed walkthrough of creating and managing ConfigMaps and Secrets practically in a Kubernetes cluster.

### ✅ Steps Covered in the Demo:

1. **Create a ConfigMap using YAML**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: test-cm
data:
  DB-Port: "3306"
```

Then apply using:

```bash
kubectl apply -f cm.yaml
```

2. **Describe the ConfigMap**

```bash
kubectl describe configmap test-cm
```

3. **Reference the ConfigMap in a Pod as an Environment Variable**

* Modify your deployment YAML to include:

```yaml
env:
- name: DB_PORT
  valueFrom:
    configMapKeyRef:
      name: test-cm
      key: DB-Port
```

* Apply the deployment:

```bash
kubectl apply -f deployment.yaml
```

* Verify inside the pod:

```bash
kubectl exec -it <pod-name> -- env | grep DB
```

4. **Update the ConfigMap and Understand the Limitation**

* Edit `cm.yaml` and change port from 3306 to 3307
* Apply it again:

```bash
kubectl apply -f cm.yaml
```

* You’ll notice the pod does **not** auto-update the env variable (because env vars are set at container start).

5. **Using ConfigMap as a Volume Mount (Dynamic)**

* Define volumes in deployment:

```yaml
volumes:
  - name: db-config
    configMap:
      name: test-cm
```

* Mount into container:

```yaml
volumeMounts:
  - name: db-config
    mountPath: /opt
```

* Verify the file inside the pod:

```bash
kubectl exec -it <pod-name> -- cat /opt/DB-Port
```

* Now update the ConfigMap value again, apply it, and check the file—it reflects the latest value without restarting the pod.

6. **Create Secret via CLI**

```bash
kubectl create secret generic test-secret --from-literal=DB-Port=3306
```

* Describe and decode the secret:

```bash
kubectl describe secret test-secret
kubectl get secret test-secret -o jsonpath='{.data.DB-Port}' | base64 --decode
```

7. **Use Secret in Deployment (Similar to ConfigMap)**

* Replace `configMapKeyRef` with `secretKeyRef` in deployment YAML

```yaml
env:
- name: DB_PORT
  valueFrom:
    secretKeyRef:
      name: test-secret
      key: DB-Port
```

> 💡 Tip: You can mount Secrets as volumes too, just like ConfigMaps

---

**Conclusion & Homework**

Today we explored both theory and hands-on use of ConfigMaps and Secrets in Kubernetes.

🧠 **Assignment:**

* Repeat the ConfigMap exercise using a **Secret** instead (DB password)
* Use `secretKeyRef` inside the deployment
* Try both env and volume mount methods

💬 If you have any questions, drop them in the comments. Don't forget to like, share, and subscribe!

Thank you! See you in the next video 👋
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

This section is a detailed walkthrough of creating and managing ConfigMaps and Secrets practically in a Kubernetes cluster.

### ✅ Steps Covered in the Demo:

1. **Create a ConfigMap using YAML**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: test-cm
data:
  DB-Port: "3306"
```

Then apply using:

```bash
kubectl apply -f cm.yaml
```

2. **Describe the ConfigMap**

```bash
kubectl describe configmap test-cm
```

3. **Reference the ConfigMap in a Pod as an Environment Variable**

* Modify your deployment YAML by adding the `env` section inside the `containers` specification of the `spec` section. Here's how the structure looks:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sample-app
  template:
    metadata:
      labels:
        app: sample-app
    spec:
      containers:
      - name: sample-container
        image: your-image-name
        env:
        - name: DB_PORT
          valueFrom:
            configMapKeyRef:
              name: test-cm
              key: DB-Port
```

* This `env` section should be placed **under the container definition** where you specify image, ports, etc.
* Replace `your-image-name` with the actual container image you want to use.

```yaml
env:
- name: DB_PORT
  valueFrom:
    configMapKeyRef:
      name: test-cm
      key: DB-Port
```

* Apply the deployment:

```bash
kubectl apply -f deployment.yaml
```

* Verify inside the pod:

```bash
kubectl exec -it <pod-name> -- env | grep DB
```

4. **Update the ConfigMap and Understand the Limitation**

* Edit `cm.yaml` and change port from 3306 to 3307
* Apply it again:

```bash
kubectl apply -f cm.yaml
```

* You’ll notice the pod does **not** auto-update the env variable (because env vars are set at container start).

5. **Using ConfigMap as a Volume Mount (Dynamic)**

* Define volumes in deployment:

```yaml
volumes:
  - name: db-config
    configMap:
      name: test-cm
```

* Mount into container:

```yaml
volumeMounts:
  - name: db-config
    mountPath: /opt
```

* Verify the file inside the pod:

```bash
kubectl exec -it <pod-name> -- cat /opt/DB-Port
```

* Now update the ConfigMap value again, apply it, and check the file—it reflects the latest value without restarting the pod.

6. **Create Secret via CLI**

```bash
kubectl create secret generic test-secret --from-literal=DB-Port=3306
```

* Describe and decode the secret:

```bash
kubectl describe secret test-secret
kubectl get secret test-secret -o jsonpath='{.data.DB-Port}' | base64 --decode
```

7. **Use Secret in Deployment (Similar to ConfigMap)**

* Replace `configMapKeyRef` with `secretKeyRef` in deployment YAML

```yaml
env:
- name: DB_PORT
  valueFrom:
    secretKeyRef:
      name: test-secret
      key: DB-Port
```

> 💡 Tip: You can mount Secrets as volumes too, just like ConfigMaps

---

**Conclusion & Homework**

Today we explored both theory and hands-on use of ConfigMaps and Secrets in Kubernetes.

🧠 **Assignment:**

* Repeat the ConfigMap exercise using a **Secret** instead (DB password)
* Use `secretKeyRef` inside the deployment
* Try both env and volume mount methods

💬 If you have any questions, drop them in the comments. Don't forget to like, share, and subscribe!

Thank you! See you in the next video 👋
**Day 41 - Kubernetes Config Maps and Secrets Deep Dive**

---


### 8. **Use Secret as Volume Mount in Deployment**

Define secret volume in the `spec` section:

```yaml
volumes:
  - name: secret-volume
    secret:
      secretName: test-secret
```

Mount into container under `volumeMounts`:

```yaml
volumeMounts:
  - name: secret-volume
    mountPath: "/etc/secrets"
    readOnly: true
```

Verify contents inside the pod:

```bash
kubectl exec -it <pod-name> -- ls /etc/secrets
kubectl exec -it <pod-name> -- cat /etc/secrets/DB-Port
```

---

### 9. **Create and Use TLS Secret (Advanced Option)**

If you have TLS certificates (e.g., for Ingress or HTTPS apps):

```bash
kubectl create secret tls my-tls-secret \
  --cert=path/to/tls.crt \
  --key=path/to/tls.key
```

This will generate a TLS-type secret which you can reference in:

* Ingress YAML files for HTTPS termination
* Pods needing mounted SSL certificates

Example usage in Ingress:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: tls-example
spec:
  tls:
  - hosts:
    - yourdomain.com
    secretName: my-tls-secret
  rules:
  - host: yourdomain.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: sample-service
            port:
              number: 80
```

Apply it with:

```bash
kubectl apply -f ingress.yaml
```

> 🔐 TLS secrets are commonly used with Ingress controllers like NGINX, Traefik, etc.

---

**Conclusion & Homework**

Today we explored both theory and hands-on use of ConfigMaps and Secrets in Kubernetes.

🧠 **Assignment:**

* Repeat the ConfigMap exercise using a **Secret** instead (DB password)
* Use `secretKeyRef` and **also try mounting it as volume**
* Create a TLS secret and use it in a sample Ingress

💬 If you have any questions, drop them in the comments. Don't forget to like, share, and subscribe!

Thank you! See you in the next video 👋
