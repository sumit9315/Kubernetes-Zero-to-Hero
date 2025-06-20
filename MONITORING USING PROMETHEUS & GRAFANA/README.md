### Day 42: Kubernetes Monitoring with Prometheus & Grafana – Full Transcript (Formatted)

---

**📢 Welcome & Introduction**

Hello everyone, my name is Abhishek and welcome back to my channel. Today is Day 42 of our complete DevOps course, and in this class, we'll be learning about **Kubernetes Monitoring**.

This class is not just going to be theory — I’ve also created a **GitHub repository** that contains practical installation steps. All the commands used in the demo are there. The reason I created this repository is because we will be doing a lot of hands-on with Minikube, and many people ask if I can share these steps. I plan to enhance this repo in the future with:

* Advanced Kubernetes monitoring
* Writing custom metric servers

Please ⭐ star the repository to keep track of updates.

---

### 📌 Agenda for Today

1. Why do we need monitoring?
2. What is Prometheus?
3. What is Grafana?
4. Installing Prometheus and Grafana
5. Live demo using Minikube
6. Viewing metrics and dashboards
7. Fetching custom application metrics

---

### 🎯 Why Monitoring?

Let’s assume you’re a DevOps engineer in an organization using Kubernetes.

* If you only have **one** Kubernetes cluster, it’s manageable.
* But what happens when multiple teams use the same cluster?
* You start hearing complaints like:

  * "My deployment isn’t receiving requests."
  * "The service was inaccessible for a while."

Now imagine this happening across **Dev**, **Staging**, and **Production** clusters.

That’s where **Monitoring** becomes essential.

---

### 🔍 What is Prometheus?

* Open-source tool originally created by SoundCloud.
* Pulls metrics from the **Kubernetes API server** (and other sources).
* Stores these in a **Time Series Database** (TSDB).
* Exposes a **PromQL query interface**.
* Includes a component called **AlertManager** to send alerts to Slack, Email, etc.

**Prometheus Architecture:**

* Prometheus Server fetches metrics from `API Server /metrics`
* Metrics are stored with timestamps
* You can query via Prometheus UI or use APIs

---

### 📊 What is Grafana?

* Visualization layer
* Prometheus is the data source
* Grafana pulls from Prometheus and shows visual charts
* Grafana supports multiple data sources: MySQL, ElasticSearch, etc.

---

## 🧪 Section 4: Live Demo Walkthrough

---

### 🧱 Create a Kubernetes Cluster

We’ll use **Minikube**. Although I prefer **Kind** for lightweight tasks, Minikube is better when demos require more memory or networking.

**Start Minikube with Hyperkit (on Mac):**

```bash
minikube start --memory=4g --driver=hyperkit
```

> 💡 If you're using Docker, networking may need more configuration.

---

### ⚙️ Install Prometheus

Go to the GitHub repo’s `installation/prometheus` folder.

Add the Prometheus Helm repo:

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

Install Prometheus:

```bash
helm install prometheus prometheus-community/prometheus
```

Check if Prometheus is running:

```bash
kubectl get pods -n default
```

Expose Prometheus via NodePort:

```bash
kubectl expose service prometheus-server --type=NodePort --name=prometheus-server-ext
```

Get IP and Port:

```bash
minikube ip
kubectl get svc
```

Access Prometheus at:

```
http://<minikube-ip>:<node-port>
```

---

### 📦 Understand `kube-state-metrics`

* Kubernetes API exposes basic metrics.
* But for advanced metrics like deployments, replicas, pod status, you need **kube-state-metrics**.

Expose the kube-state-metrics service:

```bash
kubectl expose service prometheus-kube-state-metrics --type=NodePort --name=kube-state-metrics-ext --target-port=8080
```

Access it:

```
http://<minikube-ip>:<kube-state-node-port>/metrics
```

---

### 📈 Install Grafana

Go to `installation/grafana` folder in the repo.

Add Helm repo:

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

Install Grafana:

```bash
helm install grafana grafana/grafana
```

Get Grafana admin password:

```bash
kubectl get secret --namespace default grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

Expose Grafana:

```bash
kubectl expose service grafana --type=NodePort --name=grafana-ext
```

Access Grafana:

```
http://<minikube-ip>:<grafana-node-port>
```

Login with:

* Username: `admin`
* Password: <from command above>

---

### 🔌 Connect Prometheus as Grafana Data Source

Steps:

1. Open Grafana
2. Go to **Settings → Data Sources → Add Data Source**
3. Choose Prometheus
4. Enter Prometheus URL: `http://<minikube-ip>:<prometheus-node-port>`
5. Click **Save & Test**

---

### 📊 Import Dashboard in Grafana

1. Click **+ Create → Import Dashboard**
2. Use ID: **3662** (not 3326)
3. Set Prometheus as data source
4. Click **Import**

This imports a **Kubernetes monitoring dashboard**.

---

### 🧪 Custom Application Metrics (Optional)

* Default Prometheus only scrapes API server/kube-state-metrics.
* For app-level metrics:

  1. Developers expose custom `/metrics` endpoint using Prometheus libraries.
  2. DevOps edits Prometheus ConfigMap:

```bash
kubectl edit cm prometheus-server
```

Inside `prometheus.yml`, add under `scrape_configs`:

```yaml
  - job_name: "my-custom-app"
    static_configs:
    - targets: ["<your-app-ip>:<your-port>"]
```

Save and Prometheus will start scraping that endpoint.

---

### ✅ Summary

* Setup Minikube Cluster
* Installed Prometheus & Grafana
* Used kube-state-metrics
* Visualized data with Grafana
* Learned how to scrape custom application metrics

📎 For any missing commands, refer to the [GitHub repository](#) mentioned in the video.

🙏 If you liked the session, please like, comment, and share. See you in the next video!

---
