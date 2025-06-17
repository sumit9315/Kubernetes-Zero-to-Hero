# Kubernetes Pod YAML - Full Breakdown and Field Explanation

This document explains every field in a complete Kubernetes Pod YAML with examples, alternate options, and use cases.

---

## **1. apiVersion**

```yaml
apiVersion: v1
```

**Why use:** Specifies the version of the Kubernetes API.
**Options:** `v1` (for core resources like Pod, Service), `apps/v1` (for Deployment, StatefulSet)

---

## **2. kind**

```yaml
kind: Pod
```

**Why use:** Defines the type of resource you're creating.
**Options:** `Pod`, `Deployment`, `Service`, etc.

---

## **3. metadata**

```yaml
metadata:
  name: full-featured-pod
  labels:
    app: myapp
```

**Why use:** Provides metadata to identify the resource.

* `name`: Unique name for the pod (mandatory)
* `labels`: Key-value pairs to tag and group resources

**Other fields:**

* `namespace`: Specify a namespace
* `annotations`: Add additional info (e.g., metrics)

---

## **4. spec**

Defines the behavior and properties of the pod.

### **containers**

```yaml
containers:
  - name: myapp-container
```

Each pod must contain at least one container.

---

### **image**

```yaml
image: nginx:1.21
```

**Why use:** Specifies the Docker image to run.

* `nginx` is the image name
* `1.21` is the tag (version)

**Options:** Can use `latest`, or pull from private registry.

---

### **imagePullPolicy**

```yaml
imagePullPolicy: IfNotPresent
```

**Options:**

* `Always`: Pull image every time (for dev/testing)
* `IfNotPresent`: Use cached if available (best for stable apps)
* `Never`: Don’t pull image (used for local images)

---

### **ports**

```yaml
ports:
  - name: http
    containerPort: 80
```

**Why use:** Declare what port container listens on.

* `name`: Useful when multiple ports are exposed
* `containerPort`: Must match the container's listening port

---

### **env**

```yaml
env:
  - name: ENV_MODE
    value: "production"
```

**Why use:** Set fixed environment variables in container.
**Alternatives:** `valueFrom` for config/secret key

---

### **envFrom**

```yaml
envFrom:
  - configMapRef:
      name: my-configmap
  - secretRef:
      name: my-secret
```

**Why use:** Import multiple env variables from ConfigMap or Secret.

---

### **resources**

```yaml
resources:
  limits:
    memory: "256Mi"
    cpu: "500m"
  requests:
    memory: "128Mi"
    cpu: "250m"
```

**Why use:** Controls CPU/memory allocation.

* `requests`: Minimum guaranteed
* `limits`: Maximum allowed

---

### **securityContext**

```yaml
securityContext:
  runAsUser: 1000
  runAsGroup: 3000
  allowPrivilegeEscalation: false
  readOnlyRootFilesystem: true
```

**Why use:** Enhance pod/container security.

* `runAsUser`: UID for container
* `allowPrivilegeEscalation`: Prevents container from gaining root

---

### **readinessProbe**

```yaml
readinessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 10
```

**Why use:** Checks if pod is ready to accept traffic.

---

### **livenessProbe**

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 15
  periodSeconds: 20
```

**Why use:** Checks if container is healthy. If it fails, container is restarted.

---

### **volumeMounts**

```yaml
volumeMounts:
  - name: app-volume
    mountPath: /usr/share/nginx/html
```

**Why use:** Mounts a volume to a path inside the container.
**If multiple mounts:** List multiple entries under `volumeMounts`

---

### **volumes**

```yaml
volumes:
  - name: app-volume
    emptyDir: {}
```

**Why use:** Defines volume source (e.g., `emptyDir`, `hostPath`, `configMap`, `secret`)

**If multiple volumes:** Just add more under `volumes` and match names in `volumeMounts`

---

### **restartPolicy**

```yaml
restartPolicy: Always
```

**Options:**

* `Always`: (default for Deployments)
* `OnFailure`: Restart on non-zero exit codes
* `Never`: Never restart

---

Would you like a diagram and PDF version of this file?
