## Kubernetes Ingress - Day 38 of Complete DevOps Course

**Speaker:** Abhishek

---

Hello everyone, my name is Abhishek and welcome back to my channel. Today we are at **Day 38** of our complete DevOps course. In this class, we will be learning about **Kubernetes Ingress**.

---

### Why People Struggle With Ingress

People often find Ingress tricky due to:

1. **Lack of understanding of why it is needed**
2. **Failed local implementations on Minikube or other clusters**

Even though we have a detailed video on Ingress, today we will also cover:

* **Theory**
* **Practical setup on Minikube**

---

### Prerequisite

Please watch **Video 37** on Kubernetes Services before this one.

---

### Why Do We Need Ingress?

Previously, Kubernetes **Services** helped with:

* Service discovery using label and selector
* Load balancing (round-robin) using LoadBalancer
* External access via NodePort or LoadBalancer
* Dynamic IP Issue by using label and selector

But this setup had two key problems:

#### Problem 1: Lack of Advanced Load Balancing

Traditional load balancers (e.g., NGINX, F5, HAProxy) provided:

* Sticky sessions
* Path/host-based routing
* TLS/HTTPS support
* Whitelisting/blacklisting
* WAF and more

Kubernetes Service only supports **round-robin**, lacking these enterprise features.

#### Problem 2: Cost of LoadBalancer IPs

* Every `LoadBalancer` type Service creates a **static public IP**.
* Cloud providers charge for **each IP**.
* In companies like Amazon for 1000 of application ,we may create 1000s of services, so it will create 1000 of static ip and static ip are chargeble so this becomes expensive.

---

### Solution: Kubernetes Ingress

To solve both problems:

* Kubernetes introduced **Ingress** in version 1.1.
* Ingress allows you to define routing **rules**.
* It requires an **Ingress Controller** to process these rules.

---

### What Is an Ingress Controller?

* It is a **component** (usually a pod) deployed on your cluster.
* Watches for Ingress resources and configures routing logic.
* Examples:

  * **NGINX Ingress Controller**
  * **F5, HAProxy, Traefik, Ambassador**

---

### Ingress Architecture

1. You write an **Ingress resource** (YAML)
2. You install an **Ingress Controller** (e.g., via Helm)
3. The controller reads the Ingress resource and configures the load balancer

This way, Kubernetes delegates routing logic to the controller.

---

### Ingress Use Cases

* **Host-based routing:**

  * `app1.mycompany.com` → Service A
  * `app2.mycompany.com` → Service B
* **Path-based routing:**

  * `/login` → Auth service
  * `/products` → Product service
* **TLS termination**
* **HTTPS offloading**

---

### Practical Setup

#### Step 1: Confirm Service Works

```sh
minikube ip
kubectl get svc
curl http://<minikube-ip>:<nodeport>/demo
```

#### Step 2: Create an Ingress Resource

Example:

```yaml
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-example
spec:
  rules:
  - host: foo.bar.com
    http:
      paths:
      - path: /bar
        pathType: Prefix
        backend:
          service:
            name: python-django-app-service
            port:
              number: 80
```

Apply:

```sh
kubectl apply -f ingress.yaml
```

Check:

```sh
kubectl get ingress
```

> You will see no external address unless you install the Ingress Controller.

#### Step 3: Install NGINX Ingress Controller on Minikube

```sh
minikube addons enable ingress
```

Check:

```sh
kubectl get pods -A | grep ingress
kubectl logs -n ingress-nginx <controller-pod-name>
```

You should see the message:

> Synced configuration for ingress-example

#### Step 4: Update /etc/hosts (Local Only)

```sh
sudo nano /etc/hosts
# Add line:
192.168.64.11 foo.bar.com
```

> Replace with your actual Minikube IP

#### Step 5: Test Host-Based Routing

```sh
curl http://foo.bar.com/bar
```

You should see the application response.

---

### Additional Ingress Features

Search the Kubernetes documentation for:

* `TLS` based Ingress
* `wildcard` domains
* Advanced annotations

---

### Production Tips

* In production, use Helm to install Ingress Controllers.
* Use real DNS (e.g., `app.company.com`) instead of `/etc/hosts`

---

### Conclusion

* Ingress solves **two major problems** in Kubernetes:

  * Missing enterprise-grade load balancing features
  * Cost of static IPs for each LoadBalancer service
* Always remember to install an Ingress Controller
* Configure Ingress resources based on your routing needs

---

### Next Steps

If you want deeper understanding:

* Watch the full-length Ingress video (linked in the description)
* Try out:

  * TLS termination
  * Path + host-based rules
  * SSL passthrough

If you liked the video, click the **Like** button, leave your **comments**, and share with your **friends and colleagues**.

**Thank you! See you in the next video.**
Great! Since you've already added the **Kubernetes Services** demo in a clean format in your document, now let’s prepare a **step-by-step formatted extension** for **Day 38: Kubernetes Ingress** based on the full transcript you shared.

---

I’ll now update your document to include the **Day 38: Kubernetes Ingress** section directly below your Day 37 content. It will be structured like this:

---

## 📌 Day 38: Kubernetes Ingress — Complete Theory & Practical

### 🎯 Why Is Ingress Needed?

Before Kubernetes 1.1 (pre-2015), Ingress didn’t exist. Users relied solely on Kubernetes **Services** to expose their apps. However, users migrating from traditional VM-based setups identified **two major problems** with relying only on `Service`:

#### ⚠️ Problem 1: Missing Enterprise Load Balancer Features

Kubernetes `Service` (even of type `LoadBalancer`) only supports **simple round-robin** load balancing. It lacks:

* Path-based routing
* Host/domain-based routing
* Sticky sessions
* TLS termination (HTTPS support)
* IP whitelisting/blacklisting
* Ratio-based load balancing (3:7, etc.)
* Advanced security (WAF, TLS passthrough, etc.)

#### ⚠️ Problem 2: Cost of Load Balancer Per Service

Each Kubernetes service of type `LoadBalancer` creates **a new static public IP**, for which cloud providers **charge money**. In a microservices setup with 1000+ services, this is **very costly**.

---

### ✅ How Ingress Solves These

* Kubernetes introduced **Ingress Resource** to describe rules (paths/domains) for routing traffic.
* But Ingress requires a **Ingress Controller**, like:

  * **nginx**
  * **HAProxy**
  * **F5**
  * **Ambassador**
  * **Traefik**

> Kubernetes itself doesn’t do the routing logic. Ingress Controllers handle that, based on the Ingress resources you define.

---

### 🧱 Ingress Architecture Overview

```
User Request
   ↓
Ingress Controller (e.g., nginx)
   ↓
Ingress Resource (routes requests)
   ↓
Target Kubernetes Service
   ↓
Pods
```

* You define **Ingress Resources** (YAMLs with host/path rules)
* Your cluster must have an **Ingress Controller** installed
* The controller **watches Ingress resources** and routes traffic accordingly

---

## 🔧 Ingress Setup Demo on Minikube

---

### 1. ✅ Prerequisites

Make sure your app and service (NodePort) from Day 37 are already running.

```bash
kubectl get pods
kubectl get svc
```

Confirm NodePort is working:

```bash
minikube ip
curl http://<MINIKUBE_IP>:<NODE_PORT>/demo
```

---

### 2. 📄 Create an Ingress Resource

Create `ingress.yaml`:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-example
spec:
  rules:
    - host: foo.bar.com
      http:
        paths:
          - path: /bar
            pathType: Prefix
            backend:
              service:
                name: python-django-app-service
                port:
                  number: 80
```

Apply it:

```bash
kubectl apply -f ingress.yaml
kubectl get ingress
```

Note: Initially, the ADDRESS field will be empty.

---

### 3. 📦 Install Ingress Controller (on Minikube)

Use this command to install the **nginx ingress controller** addon:

```bash
minikube addons enable ingress
```

Verify:

```bash
kubectl get pods -n ingress-nginx
kubectl logs -n ingress-nginx <nginx-controller-pod>
```

You’ll see logs like:

```
Successfully validated configuration for Ingress "default/ingress-example"
```

Also, check `kubectl get ingress` — the `ADDRESS` will now be populated.

---

### 4. 🧪 Mock Domain for Local Testing

You need to map `foo.bar.com` to the Minikube IP:

Edit `/etc/hosts` (as root/admin):

```bash
sudo nano /etc/hosts
```

Add this line:

```
<MINIKUBE_IP> foo.bar.com
```

---

### 5. 🌐 Test Ingress Routing

Now access:

```bash
curl http://foo.bar.com/bar
```

You’ll see the same DevOps learning message as in Day 37.

---

### 📌 Advanced Ingress Features (Explore Later)

You can extend this by adding:

* **TLS support** for `https://`
* **Multiple paths/domains** in a single Ingress
* **Custom annotations** (rate limiting, rewrites, CORS, etc.)

For example, TLS-based Ingress:

```yaml
tls:
  - hosts:
      - foo.bar.com
    secretName: tls-secret
```

---

## ✅ Summary

| Feature               | Service (LoadBalancer) | Ingress |
| --------------------- | ---------------------- | ------- |
| Round-robin           | ✅                      | ✅       |
| Path-based routing    | ❌                      | ✅       |
| Domain-based routing  | ❌                      | ✅       |
| TLS/HTTPS termination | ❌                      | ✅       |
| Cloud cost            | High (per IP)          | Low     |
| Custom load rules     | ❌                      | ✅       |

---

If you're ready, I can **update the existing document** to append this new **"Day 38: Kubernetes Ingress"** section right after the current content.

Shall I proceed to add this to your document? Or would you like a **PDF** version instead?
Great! Here's the follow-up you asked for — a **detailed and simple explanation of TLS termination types in Kubernetes Ingress**:

---

## 🔐 TLS Termination in Kubernetes Ingress

When a user accesses your app via `https://`, TLS termination decides **where** the SSL handshake ends and **who handles encryption/decryption**.

---

### 📌 1. TLS Offloading (Most Common)

**🔹 TLS ends at the Ingress Controller** (like NGINX).
The Ingress decrypts the traffic and forwards **plain HTTP** to your backend app.

#### ✅ Benefits:

* Easy to manage certificates at one place (Ingress).
* Less load on backend apps.
* Simple to implement with NGINX Ingress + Cert-Manager.

#### 🧩 How to enable (example):

```yaml
tls:
  - hosts:
      - foo.bar.com
    secretName: tls-secret
```

Then create `tls-secret`:

```bash
kubectl create secret tls tls-secret \
  --cert=cert.crt --key=cert.key
```

> Now `curl https://foo.bar.com/bar` will go via HTTPS to Ingress, and HTTP to your app.

---

### 📌 2. TLS Pass-Through

**🔹 TLS is not terminated at the Ingress Controller.**
It is passed as-is to the **backend service**, and the pod handles decryption.

#### ✅ Use When:

* Your app needs to inspect SSL itself (e.g., mTLS, banking apps).
* You want **end-to-end encryption**, even inside the cluster.

#### ⚠️ Requirements:

* Your Ingress Controller must support TCP mode (not all do).
* Can't use path-based routing (because the Ingress can't decrypt it).

> NGINX Ingress needs a special `tcp-services-configmap` for this setup.

---

### 📌 3. TLS Bridging

**🔹 Hybrid model**.
Ingress decrypts SSL (like offloading), but **re-encrypts** before sending to the backend.

#### ✅ Benefits:

* Ingress can apply WAF/security checks.
* Backend gets encrypted traffic again.

#### ⚠️ Rare and complex:

* Used in banking/healthcare for **compliance**.
* Needs custom NGINX templates or Envoy/HAProxy.

---

## 🧪 Summary Table

| Mode         | TLS Ends At         | Backend Gets | Use Case                       |
| ------------ | ------------------- | ------------ | ------------------------------ |
| Offloading   | Ingress Controller  | Plain HTTP   | Most common (default)          |
| Pass-Through | Backend App         | Encrypted    | End-to-end security (mTLS)     |
| Bridging     | Ingress → Reencrypt | Encrypted    | Compliance + inspection needed |

---

## 🎯 Real-World Tip

* Use **TLS Offloading** with **Cert-Manager + Let's Encrypt** for 95% of apps.
* Use **TLS Pass-through** when security is **strict** (e.g., government, finance).
* Only use **Bridging** if you know exactly why you need it.

---

Let me know if you want:

* 🛠 A working example of Cert-Manager + Let's Encrypt
* 📄 How to implement TLS pass-through with NGINX or Traefik
* 🔄 How OpenShift routes handle TLS differently

Ready when you are!
---
Transcript


Search in video
Intro
ingress controllers so let's quickly jump on to this so before we understand ingress i would
slightly set up a context on the service i know this most of you already know about
kubernetes services but i just i'll just give you a brief overthrow and then we can quickly jump onto the
ingress part so it's just an abstract way of exposing
an application that's running as pods so let's assume that i have a kubernetes
cluster and in the kubernetes cluster let's say that i have deployed a
checkout application as a pod so as soon as you do that the container network interface so you
get allocated with an ip address so let's say that this checkout application or this checkout port is assigned with
an ip address called 172.16.1.15 and then there is one more application
called payments similarly it also has another ip address and
for some obvious reasons checkout has to you know communicate with payments
and to probably to complete the transaction or something like that
so the first thing that it would do is uh it can connect
to the payments using the uh cluster ip that it is associated with the port uh using the pod endpoint but
you know the problem with uh this thing is that kubernetes the ip address that generated
is dynamic so for some reasons if this port goes down so next time it would come up it would
come up with a different ip address so what what happens in that case is uh you know the
application the checkout application which is trying to talk to the payments would uh obviously receive a 404 or some
sort of hdb status quote because your payments is on a totally different ip address
Kubernetes Services
so to avoid this problem uh you can make use of kubernetes services
so like we discussed before it's just a proxy and so it identifies the required
application or the pod using the labels and selectors so this way
what you can do is uh you can use make use of the service and ensure
that your you don't have a downtime or earlier the checkout which is trying to access
the payments that doesn't receive 404 are not found so the way you can uh create this
kubernetes services so you can uh create them in tv like predominantly you can do it in
these three different ways there are other ways as well but yeah these are the most used so you can do it
you can create a service as cluster ip you can also create this service as a node port mode and
you can do it as a load balancer service so let's try to see
what happens when you create this service as a cluster ip so when you do this as a cluster ip
you know the service ip address or the ip range stays within the cluster so someone who is trying to
access like if someone wants to access uh not the payments but some sort of catalog directly
so the cluster ip wouldn't be accessible so you cannot reach using this service
and the other way of doing it is uh i mean if you want to expose your service outside you can probably do a note port
load balancer or ingress so before i jump to ingress let's see why we need
ingress because if something can be done without ingress then why do we have to go to english
because i was saying that using notepad and load balancer you can still access your application
from outside the cluster then what is the point of using an ingress so uh
if you create uh you know if you create this service in a node port mode so what typically happens is that you can access
your kubernetes pod or your service using the node port
so the cube proxy what it typically does is uh it has a node ring the port range
so it's in the 32 000 range so it allocates one of these ports it binds
one of these ports with your service and it updates the ip tables
i don't want to go into the details of the different queue proxy modes so let's restrict to ib tables so
it updates the ib tables saying that in the pre-routing rules that if somebody is trying to access
uh the node name followed by this specific node port forward the request to the payment service as i mean which
eventually moves to the payment application so this uh this way you can access
your application from outside the kubernetes cluster using the node port but the drawback is that uh you know even
sometimes your node might also not be accessed from outside are
because this port is i mean because this ports come in a random range like you don't know uh
predefined that what would be a node port b so you cannot expose multiple
ports on your firewall like you cannot say that my services would
be created within this node port range so expose all of these ports so that would cause you a problem because you are
exposing a firewall on your specific node and like you are inviting people to
get into your kubernetes cluster so that is one of the drawbacks as using node port and
the other thing is that like i told you using node port you cannot i mean if somebody is not able to access
this node range or the node i p range so you have the same problem as cluster ip
that uh they cannot actually talk to your kubernetes service so the other thing is load balancer uh
probably there might be a lot of comparison between load balancer and ingress more than the node balancer and
the uh sorry then the node port and ingress so load balancer service is typically
something that creates an external ip address for you let's assume that i have my kubernetes cluster on aws or i'm
using a kubernetes managed service of aws that is eks and i expose my service as a load
balancer so what happens is that the cloud controller manager the
control plane component of kubernetes so it creates or it generates an external
ip address for you uh in two to three minutes after creating your service and using this external ip address you can
uh you can access your service so now you don't have the problem of uh
like you know the ip address is not in the range or you cannot access the ip address so if that is the case
so uh like you know i have one question here that uh if
is the load balancer service type like the one that we are discussing about is it only restricted to the cloud
providers so uh like i was talking about aws or eks so
is the load balancer service type only managed to them uh only restricted to aws azure or any
other cloud providers or can you also do it on your uh personal uh server or on
the bare metal or in your laptop huh
yeah so there is a uh implementation uh that can do that for you so previously or predominantly it used to be uh only
restricted to the cloud providers but there is a project
there is a cncf project called metal lp so using this you can actually
install this on your bare metal or on your on-premise servers and you can expose
your service as a load balancer but you need to do some sort of configuration and this is still a
not a complete project or it's still in an amateur state but you can do load balancer type
service on your on-premise servers as well so now the most important question is if
load balancer service type can do the things for you then why use a ingress resource
because i was telling you that using load balancer you can expose
you get an external ip address and you can also get a static external ip address which doesn't change
so in in such cases why you need an ingress resource
so the reason for that is uh there are two things one is let's assume
we are talking about large scale of services uh if i am [Music]
i have an ecommerce site and i'm talking about two to three hundred services that
needs to be exposed so if you want to do that for each and every service uh the load balancer type
so what typically happens is that you create two to three hundred static
external ip address for which you will be charged by your cloud provider and uh that could go into huge huge costs uh
instead uh you can create an ingress resource and you know if you have a single ip address
like probably you want to manage all your services using a single ip address ingress can solve that problem
and the other problem that ingress can solve is uh it defines routing for your
specific services or you know you can define using ingress you can
expose your cluster outside and also you can define routing for your services
probably you can do coast based routing you can do password-based routing or session based cookie or you know
because ingress comes with i mean when you create an ingress resource uh there is a ingress controller which is
actually listening to this ingress resources and there is a load balancer as well so
you are equipped with a a lot of additional features uh you get the features of uh
like you know path based force base or you can do a web application firewall or
you can do a lot of things which we'll be discussing going ahead but this is the context that i wanted to set like what
is the use case of ingress because uh if you have node port or load
balancer type services why you have to go for ingress so this is the preliminary context that i
wanted to set okay so now
i'll be talking about ingress so i'm not looking at the chat if
someone uh posted a question or something probably i can take it at the end
okay so uh this is the uh i've just taken this picture from the
kubernetes docs so if you look at this specific picture to explain what an ingress is about
so you have a client uh you have ingress resource which defines the routing rules
for your services so probably you can say that my service can be accessed using
food.example.com or you can say that i have 10 services and each of them can be accessed on
different context paths on one of your website domains so such things can be managed using an
ingress resource so what you need to do additionally is apart from creating a service you also have to create an uh
ingress dot yaml or an ingress manifest i'll also show you uh so we'll also be
doing some uh sort of uh demo or uh some sort of uh practical
exercises to understand uh inverses more
Ingress Controllers
so this is the typical functionality so ingress cannot work directly on its own so if you just
create an ingress resource on your kubernetes cluster uh without an ingress controller it would be left as an orphan
because nobody understands what it is so whenever you want to use ingress
you need to create an english controller there are wide range of ingress controllers so you
can look for the ingress controllers supported ingress controllers in the kubernetes official page so you
have i think you have close to 30 to 40 which are officially supported ingress
controllers but there are other implementations as well so what an typical ingress controller does is
whenever you create an ingress resource this ingress controller would watch for the english resource and you know it
simply updates or configures the load balancer so let me take an example
to make you understand more let's assume that uh i'm using an nginx
ingress controller so in that case what i typically do is on my kubernetes cluster i go ahead and install nginx
ingress controller probably using helm charts or plane manifest or something
and once i install this uh ingress controller nginx would engineering singles
controller would continuously watch for the ingress resources on this specific cluster and it updates the uh i mean
nginx.conf enginex.com is uh the place where nginx uh updates all its routing
details so along with your ingress controller the enginex application is also deployed so
if if it is some other load balancer then probably if it is an fi load balancer or if it is a
citrix or any other load balancer so what their invest controller would do is it would also deploy their load balancer
along with the ingress controller so sometimes saying that some of the load balancers
some of these load balancers can sit inside the cluster or outside the cluster
so for example if you are taking the example of nginx so nginx deploys as a
pod inside your kubernetes cluster so uh you don't have to do any additional
configurations but for some of these english controllers which probably i i don't expect many people to
use nginx in their production uh some some clients might do but
most of them would go for enterprise solutions so in such cases uh the load balancer might not be sitting inside
your kubernetes cluster because those load balancers might not have a containerized solution right so i was
working for uh fi before to this and uh so it itself is a very huge load balancer and it did not have a
containerized solution so if i sits outside your kubernetes
cluster and the way it can access the resources inside your kubernetes cluster is using a vxlan tunnel so you have to
do some uh configurations before you actually deploy your or before you get started with your ingress controller
so the whole point of saying this is that um so the configuration of ingress controller varies from one ingress to
other i mean from one controller to another control another controller so it might be simple in some cases and it
might be pretty complex in the other cases
so this is a very simple ingress resource how how a basic ingress resource would
look like so there is no uh who's definite there is no host definition or there is nothing associated with this
basic ingress resource all that you have is you define the kind as an ingress
and this is your api version so uh previously the eps version used to be
extensions but uh in from kubernetes 1.21 i believe so the extensions is
deprecated now that you you can only use the networking.kx.io
and you define the kind as ingress and then you define inside your spec you say what is your service
on which port your service can be accessed
Ingress Routing
sorry for that yeah so after that uh you like i told you with ingress you can do
host based as well as path based routing so i think we can jump
on so i have some
Ingress Demo
okay so um can somebody confirm that you are looking at my terminal
yes you can see your domain okay thank you thank you so uh for the purpose of demo i
got a couple of services running
so there are these are the two services that are running so now what i'll try to do is
that i'll try to show you that
let's start with the sample a very simple ingress resource sorry
so this is a very simple ingress resource and for this ingress resource i just have uh
you know i just have defined a host and what i'm expecting from this ingress resource is that
my service uh which i've mentioned which is mentioned as a http hyphen svc on
port 80 should only be accessed if the user uh provides the host name as
food.bar.com so here what i'm trying to do is i'm trying to define the traffic
rules for my service right so if it was only a only a service
you can simply access this service on the service ip address but what i'm doing with this ingress is that i'm
defining some uh rules for the user to access this specific service
so now let me try to create
okay so uh one more thing that comes to my mind when i when i have to talk about this is so if you
see about this if you see this ingress resource there is no ingress class i haven't
defined any ingress class for this ingress resource but
but the router has assigned or the ingress controller has assigned the ip address
to my ingress right so the reason for this is because uh what i've done before to the demo is that
this is my uh ingress controller and in the ingress controller configuration i simply mentioned that
class so i simply said uh watch ingress
without class so this way what my ingress controller would do is it would watch for the
ingresses which doesn't have any class or you know uh it simply would watch all
the ingress resources on the cluster so if if i mention this
like you know if i specify this ingress class as probably abhishek then only the ingress
resources with ingress class name as abhishek would be watched by this ingress controller
so why why you need ingress classes is uh basically because uh you can
do multiple ingress controllers in the same kubernetes cluster probably similar ingress controllers or different ingress
controllers as well so probably you have a combination of engine x and h proxy or you have
multiple engine x ingress controllers which are used by different teams so in in such cases you need a bifurcation for
these resources who has to watch which ingresses so for the for that obvious reason you have ingress class names
okay so qcdl get ingress and now what i'll try
to do is i'll say girl
so it says uh who's not found uh ideally it should have been accessed uh on this
ip address but it was not access it is not accessible because i did not mention
the host name so what i'll do is in the headers i'll simply pass the host name
as and now
i'm able to access right so this is because uh because the rule that i've defined is
Pathbased Routing
alert traffic on that specific service or i'm telling the ingress controller to
only accept traffic if the host name is food.bar.com so i just passed it in the headers and
that is the reason why ingress ingress controller has you know it simply allowed me to access
the defined service now uh similarly you can uh
you can do path-based routing so what i mean by path-based routing is
that so the same use case but now what i'll try to say is that
if user tries to access food.bar.com with context root or with
the path as slash first then route the traffic to http service
and if the user tries to access food.bar.com with path as second then
you know route the traffic to the uh miyab service so let's see how how this works right
so firstly let me delete the old one
now again uh the ingress controller would take uh some time to uh get the ip
address i it was it was pretty quick but yeah so now let me try to uh access the
scene same url so now again you would receive 404
because i haven't defined any path and in the ingress definition i told the ingress controller to only accept
when the user is trying to access on one of these parts like it should be slash
first then okay
dot com
change
let's do this
okay let me try to access it on second
okay probably i think that service might had some issues probably i i don't know
uh the pod probably is not reachable or something don't want to waste time on debugging
Ingress Controller
that thing but uh so if you see now uh i mentioned some path and depending on the
path it uh sent or it routed the traffic to a different uh application so now how
how are these things happening so let's try to look at the logs so
this is the ingress controller uh so the english controller is created in uh this specific name space called ingress
engine x and uh because the ingress controller that i'm using is uh nginx so
it updates this configuration in the nginx.com so
okay it's too big
yeah so if you see here uh the nginx.conf is updated with the
uh the configuration for food.bar.com and it says that if someone tries to access the location on second then uh
forward the request to the specific service so this is how the ingress related configuration is updated so it
says if the location is second then forward the request to service on the specific port
so similar to this uh depending upon the type of the load balancer that you're using if uh let's assume that we are
deploying argo cd on uh openshift cluster and for some reason uh your argo
cd is not accessible so what you would do is that you would go to the ingress operator's namespace on your openshift
cluster and you can watch for the hr proxy logs sorry hr proxy uh configuration file so
in terms of nginx this is the configuration file and in terms of hp proxy
i don't remember the name but it should be in one of these similar locations it should be probably slash
etc proxy hproxy.conf so that is where you would actually debug and try to
understand the issue by so if you if you remember uh probably
last week or sometime we identified an issue that when we deploy
argo cd uh using an ingress on the openshift cluster the hp proxy was not
i mean the user was not able to forward the or accept send the request so that was because
the hr proxy did not even identify this ingress resource and nothing was updated in the hp proxy
configuration so that was easily identified when you look at the hp proxy conf because there was
no entries related to that specific ingress research so then we came to know that uh okay
probably because of the missing ingress class annotation or something
okay so uh this is the thing and you can also do uh wildcards with ingress so i
think this is uh probably if you're if you're using it in organization uh this is one
of the most used uh because like for example you are
on google.com or something then you have slides.google.com or you have
docs.google.com mail.google.com so instead of you know providing for each and every uh service this can be for the
tls as well so in this case this happens to be a host for the rules but uh if you
are doing a secure ingress so you can also provide the wildcards for the tls configuration as well probably in
terms of you have wildcard certificate there is a very good chance that you get a wildcard certificate from your
certification authority so in such cases you can also provide instead of creating
multiple ingress resources you can simply create one ingress resource with the wildcard
and when you create such thing make sure you use the double quote so that you don't waste 30 minutes like
me so if you do the wildcard
which you can get ingress
so now let's get the ingress one more time
okay let's wait for the congress to identify
yeah so now the ingress controller has identified this specific ingress resource and it has updated the
ip address so now i can access the same thing
and i can say food.bar.com or i can also say
example.bar.com similarly i can uh pass anything in the headers so it can
be any host that is uh succeed i mean yeah that is succeeded by
bar.com yeah so this is about the wild cards so this is one of the major use cases of uh
Unsecure Ingress
non-secure ingress so we haven't gone to the secure ingresses but if you're using
unsecure ingress so you can achieve all these things with the static http as well like
the things that we have seen you can do path based or you can do code based you
can do wildcard and the other thing that you can also do is uh you can also use uh basic authentication that is
for example you want to say that uh okay anybody cannot access my host or you know if
somebody is trying to curl to the argo cd then he should be a legitimate user so
only the ones who have access or authorization to argo cd can only access my
domain so in such cases what you can do is in your ingress you can simply annotate
your ingress using the basic auth and you can say that
you know you can provide this ingress with the list of users who has who can access your
food.bar.com or who can access your service and so in such cases
what happens is that only the users who have access to your website can talk to your service
yeah i think uh i mean if we it wouldn't make much sense to show that
as well because we are running out of time but the thing is that you can simply annotate the ingresses in that way and
you can achieve the basic authentication yeah if we go back to the slides one
more time so this is all about unsecure ingresses so we haven't uh done
anything with respect to uh secure but so now if you look at tls
Secure Ingress
so i think uh jan has mentioned a lot about tls the other day
so similarly if you apply the same concept to ingress so you can do uh
you can basically add security to your ingress or you can add security to your services so there are different ways how
you can do it so in the other examples that we were looking it was only about http uh all the services were on port 80
and all the traffic that we were talking about is on port 80 but what if i want to enable uh security or
what if i want to make my uh whole process secure so the first thing that you can do is you
can use a ssl passthrough so ssl passthrough is a way basically the entire
client to server request is completely encrypted and your load balancer that is in
that is in the between uh doesn't do much it just acts as a pass-through and your request just passes through the
load balancer without encryption or decryption at the load balancer level
so what happens in this case is that uh okay so this makes i mean at first this
looks very secure and it is indeed very secure but there are some problems with this uh approach that is uh you have the
load balancer uh in between uh in some cases it can be a very powerful load balancer it can it can also act as an
epa gateway but because you are restricting your ingress to pass through you cannot take advantage of your load
balancer right so for example you have a load balancer that can do a web application firewall or you have a
load balancer that can do path based routing or session based routing but
if you are doing pass through so you have to compromise on all of these features because uh load balancer
mainly act as a proxy and nothing more than that and one of the other disadvantages is
that you know a hacker probably instead of client there is a hacker who is trying to send
request to your server and because it's a pass-through ingress your
load balancer doesn't even look at the packet or it doesn't even look at the request that is coming to it and you
know there is a very good chance that load balancer misses the request and the
hacker can get direct access to your server right so this is one of the major
drawbacks at least according to me that uh you have a load balancer but uh because it is not decrypting the packets
or it is not re-encrypting the packet uh so there is a good chance for the
individual to directly reach or the hacker to directly pass a malware in the request and reach to the server
and the other thing is that so this is also one of the important things according to me that
let's assume that you have a service and during christmas or during one of your
holidays your service is receiving exceptionally uh high requests probably
it's into millions or something more than that so in such cases because your service has to take care of uh
decryption right uh load balancer is not doing anything for you so every request
that is coming to your service is an encrypted request and your service has to decrypt each of these requests so
because of that what happens is that uh you know your service you
you you see the latency so that is what i was trying to say that because your service has to do a lot of
work so every time a request comes it has to decrypt the request and it has to process the request so
that is one of the major reasons even uh like if you uh see a latency during uh holidays or you
know when most of the people are trying to access the application this is one of the reasons why you see the latency that
your service is doing a lot even though you have a load balancer in front of your service
SSL Offloading
so these problems can be handled by ssl offloading so ssl offloading is the one
of the other kinds of tls that you can do with ingress so i'm not saying it solves all
of the problems but it solves one of the problem that i discussed at the end that
is if you have ssl offloading enabled on your ingress then you know decryption
happens at your load balancer level and load balancer sends a plain http traffic to your service so that way your service
is not uh in the you know uh not receiving a lot of load so it simply
resets the request and it's about uh the how you wrote your application so if your application can handle the request
then that's it you know if you have use multi-threading or whatever
aspects uh you have followed in your application then that's well and good so
because load balancer is doing the decryption for you so at least the ssl kind of offloading is done
but one of the major concepts of ssl offloading and ssl offloading is highly
not recommendable when security is one of your key aspects because you know uh there is a heavy
chance of managing middle attack so because your load balancer is simply sending a
plain hdp traffic for your application and most of the times your load balancer
is sitting at the edge of your network right so you know different types of load balancers actually behave in different
ways some of the load balancers sit at the edge of your whole network so if your load balancer happens to sit at the
edge of your network and it is simply passing a plain http traffic to your application then
uh you're almost inviting uh people to get into your application
but uh why if one aspect it has to be better than pass through is only thing is uh it is
faster like your application latency can be reduced if you can compromise on the security
pattern and the other option is ssl bridging so
SSL Bridging
ssl bridging uh it is also called as re-encrypted uh so there are different
names for these things when it comes to open shift routes which i'll be talking about but if you are doing ssr bridging
so ssl bridging is mostly similar to re-encrypt even if you look at the picture that i try to represent it is
almost similar to the pass-through i mean sorry so ssl bridging and pass-through are mostly
similar but one of the advantages of ssl bridging is that it does not send the packet
directly to the server but your load balancer does a lot of things for you in terms of ssl printing that is firstly it
would decrypt the packet that is coming from the client and then it would re-encrypt right so
what your load balancer does is every request that is coming from the client it takes the request it decrypts
the request it looks into the information of the request like what is the actual request about is
there any malware or is it a legitimate request that is coming to it or does it have to uh you know perform any
additional things like path based routing or a lot of things that a load balancer can do so it decrypts the packet it reads
the packet understands the packet and it re-encrypts the packet so
before it sends to the server it re-encrypts the packet and again it starts a secure channel with your server
so that way both the communications are secure your client load balancer is secured and your load balancer to your
application is also secured but uh so this addresses
the second pain point that i address here like if hacker sends a malicious or a malware
request request with a marvel uh in the first case uh because it was passed through it
actually landed to the server but here because the decryption was happening and load balancer is reading
the packet and if i identify that there is a malware it simply aborts the request or depending on the way that you
have configured your load balancer it can you know say that okay i don't understand this request it looks
something malicious so i'm not processing or sending this request to the server
so you know lancer can do that for you it is very secure but again uh like you
know whenever security comes into the picture then you have a lot of things happening so
again your server has to bear with the thing that it has to decrypt the packet that is
coming at it so every time a request is coming to a server the server has to
take that additional leap or it has to do the decryption edit
Comparison
so a simple comparison uh between ssl pass through ssl
offloading and ssl bridging so if you have to compare all of these things in
at one place and if you ask me what you would suggest out of these three then it's it's not a
straightforward answer it actually depends on a lot of the aspects so i try to put all of these things
at least i am but i mean when
kind of the applications we look at for example passthrough is
i mean you can use it in very less cases if you ask me pass through because uh it doesn't uh take the advantage of the
ingress controller that you're using right the only thing it does is it makes use of your interest controller as just
a proxy uh and it does some kind of uh you know
load balancing but only at the layer 4 that is at the tcp level so
if you're if you want application load balancing and you know
definitely pass through is not the option or ssl password is not the option you can actually go for the ssl bridge
or ssl re-encrypt and if you want
if you can compromise a bit at the security which is again not the right thing to do
but if some of the applications doesn't require uh security uh as one of its key aspects
then definitely ssl offloading makes a lot of difference so if security is not
your uh thing or you can compromise or you can uh accept that okay if it is
coming from my load balancer uh i trust that load balancer is doing uh
everything for me and i accept the packets that are i accept the requests that are coming from uh
load balancer then ssl offloading is the perfect choice for you and it reduces a lot of things like uh
latency can be gone and uh you know uh yeah and if you look at
the ssl pass-through like uh it's costly because uh because of the reasons that i mentioned but your connection is secure
and it's mostly used only when you require uh l4 load balancing or the tcp level load balancing
and your load balancer does not do any kind of cookie it doesn't verify any kind of cookies or anything
and yeah it is recommended only when that's why i try to put a star here it is only recommended in very few cases
whereas ssl offloading is highly unrecommendable and ssl bridge is recommended i know like
this is this slide can put a lot of questions or many people might disagree with
the things that i put here or there are different uh views on this specific
thing but uh yeah we can uh discuss on that
and uh like coming to the routes okay i have uh
i mean i have examples for all of these things the ssl edge thing and uh
how you can do uh its routing with heads with ingress as well as uh pass
through how you can do password with ingress and re-encrypt so i'll see if time permits or else we can do it in the
next session sometime and i'll try to put it in the github as well so if somebody's willing they can
try it so coming to openshift routes you know uh
Openshift Routes
openshift routes uh the terminology is slightly different so ssl offloading is called edge termination ssl bridge is
called as re-encrypt termination and ssl pass through is called as password termination but more or less the concept
is similar but the naming convention is slightly different and
if you uh try to look at uh the openshift routes there are a couple of things
TLS Secrets
personally that i don't like that is uh you cannot store uh your tls
certificates in secrets so what you have to do is you have to put them for example the second and the third if you
look at second and third the edge as well as the re-encrypt so we are passing the certificates uh as
part of your route itself so even we do this with argo cd as well if
you try to look at the argo cd crd so we support all of these fields uh a customer can update the rcd route with
all of these options but the major problem is that you know
many people don't would not put their certificates inside a or
they want to uh store them in the secrets or by putting them here you know uh they cannot actually uh store this
configuration in github this route with secrets cannot go to github because uh
for obvious reasons and so this is the issue i try to put the
issue and why uh like there are many requests many people
have been asking about this uh to support secrets with routes and
the answer for now is that using risk so the proposed work around is to use
ingress and yeah i think that is the reason why many of the customers even today use ingress
over route even when they are on openshift because ideally if openshift supports routes
they should be looking at using routes but because of these reasons there are
customers who are still using ingress on openshift
yeah and
okay so
there is one more thing that i wanted to talk about
so uh okay so in terms of uh open shift what you can do is that
if you want to secure your openshift routes so you don't have to create all the
secrets by yourself openshift supports something called as service serving certificates
so what you can do is that you can simply annotate your openshift service with
this specific annotation and uh you know openshift comes with a default
certification authority and all those are sales and certificates but it can be trusted within the cluster
and it generates a certification authority and the ca certificate is stored in a config map in the same name
space and you can mount that into your pod so if it openshift it's slightly easy but
with ingress i'll try to show you here so what you can do with ingress is that
if you want to do secure ingress or you want let me show
you an example of its termination
so i have created the client certificates as well as uh like you know i've created the ca client certificates
as well as the server certificates so that i don't take a lot of time here and if you look
at the ingress
so this is the english resource and the way we do tls with ingress is that you can simply store it in the secrets so
this is the difference that uh ingress gets over openshift that you don't have to
have your secrets or you don't have to save your secrets in the ingress resource itself you can simply create a
secret and you can uh provide the corresponding secret so in the same way what i've done is
i've created the secrets and i've updated the secrets in secrets type tls
so once i do that
so i have to delete these ingresses because uh i i have some of the names common so
sorry for that apply
so now the ingress is getting created with uh
with edge termination so what happens is uh if i have to access the
router if i have to access the uh english ingress controller then i have
to provide uh then i have to show the relevant certificates to it only then the request is accepted
so like we saw in the previous example only till the router your traffic is encrypted
so we can simply say curl
and i would say i'll simply pass the headers as host
and food.bar.com
sorry to interrupt but i think there is another session and so people are yeah sure you
think yeah no no just like okay okay got it what you can continue
here yeah so i'll just close it in a couple of minutes so uh this is how like
you know i tried to access the service but you see that it failed so i can pass
the client certificates and then that would simply
pass my request okay because it's a self-signed
certificate that i'm using so now if you see because i pass the certificates and router can simply
accept my request and so i'm able to access so i have other examples as well for the pass through as
well as uh re-encrypt but probably i'll take it in some other session uh sorry i was late by uh some time so that might
be the reason why i went over the meeting
Conclusion
thank you it was it was a great session it's really a helpful session
yeah that's all i have uh thanks do you have any questions on any specific topic that we did i had asked one in the chat
but it's mean i mean there are so many controller implementations for the same resource
type okay that is because uh you have so every load balancer feels that uh
they are better at it right so engine x feels that they are the best load balancer here proxy thinks that it is
the best and uh there are many enterprise load balancers because there are many load balancers there are many
implementations to the ingress right and everybody is trying to just fight over each other like
and uh so that's the obvious reason thanks
make sense competition yeah and even if you look at the implementations also they are very quite
different so uh even apart from ingress and routes many of these modern day ingress controllers
support custom resource definitions and they have their own uh types like because ingress is pretty much static
right the you cannot make anything more with this ingress file right so uh what these people have come
up with is uh they have created their own custom resources some of them are called ingress routes some of them are
called virtual services if you're aware of service mesh so different people call with different names and the
implementations are also quite different and yeah just one last thing that i would say is uh i showed you one of the
drawbacks with route that uh if you're talking about route you cannot secure or you cannot store this
uh route's information in secrets right that is with the predominant uh load
balancer that comes within it the ha proxy but there are many load balancer like uh fi
is one of the load balancer where it doesn't even i mean it allows a route to store these
secrets in a uh these tls certificates in a secret and it also provides the other ways like i
told you custom resource definitions or you can also put them in a different secret name or config map and you can
provide it uh in the annotations so that's the reason why uh different load
balancers are there like hf proxy is not supporting it so other load balancer is supporting it so people move to other
load balancer yeah great session um do you have your
examples your manifest checked in somewhere and yeah i'll all right i'll put them in my github and i'll try
to put it in the github's cloud clubhouse okay cool
thanks a lot everyone for taking your time out thanks thanks a lot thanks
thanks a lot
you
---


Search in video
hello everyone my name is Abhishek and welcome back to my channel so today we are at day 38 of our complete devops
course and in this class we will be learning about kubernetes Ingress so people find this concept slightly tricky
or people find it slightly difficult because of two reasons one is they don't understand why Ingress is required right
if you don't understand why Ingress is required then definitely you will find the topic complicated and the second
thing is practical implementation so people try it on their mini Cube clusters or on their local kubernetes
clusters and they will not succeed with the setup so that's one of the other reasons why people find it difficult
I've also seen few videos and we also have created a end-to-end video on
Ingress on our particular channel so I'll also share the link in the description so that you can follow the
link where we have done end to end complete practical on how to set up Ingress and all of the things don't
worry even in today's class I'm going to explain you both the theory and the Practical okay so even if you
watch this video till the end you will get a very detailed understanding on why Ingress is required and how to
practically install Ingress and try out the things so you know if you have followed our previous class on service
Deep dive you know you'll be easily able to understand today's topic so if you haven't watched the video 37 that is on
Deep dive of kubernetes services I'll highly recommend you to go and watch video number 37 on the complete devops
course and only then come back so that you understand the concept of Ingress very well okay now without wasting any
time because we have to cover a lot of things in this specific video let me jump onto the video okay so firstly what
is ingress so you must be asking me that Abhishek
in the last class we used kubernetes services and service was offering me a lot of
good things right so I explained to that service was offering you service Discovery mechanism on kubernetes so it
is solving this problem it was also doing a load balancing for you right services for doing the load balancing we
have seen using the cube shark utility as well in the last video and it was also exposing the applications to
external world then why you need a tool like Ingress or why you need a concept like Ingress and what problem is it
solving so before 2015 uh December I guess or November so that was before
kubernetes version release 1.1 Ingress was not even there okay so
people were using kubernetes without Ingress that was that means people were using kubernetes with just service
concept okay and Ingress without Ingress so what they used to do was similarly uh
like we were doing till the last class so people used to create a deployment which would create a pod right and
additionally because you are creating a deployment you you will get Auto healing and auto scaling right these features
and then you will create a service on top of it so that you can expose your application within your kubernetes
cluster or outside the kubernetes cluster using the load balancer that is using the load balancer mode of
your service but there are some practical problems which people realized after using kubernetes okay so once
people started using kubernetes so obviously these users who migrated to kubernetes were migrating from the
Legacy systems like like people used to have virtual machines or physical servers on top of that they used to
install their applications okay and what people used to do was they used to use a
load balancer so these load balancers were something
like you know people used to use uh engine X engine X or you know people used to use
fi load balancer or any other load balancers that they want to use on their
virtual machines or physical servers okay and these are some Enterprise load balancers
okay so what is enterprise load balancing so they offer very good load
balance and capability load balancing capabilities like for example you can do ratio based load balancing that is you
can say uh send three requests to pod number one seven request to pod number two you don't have pods in the virtual
machines but just for your understanding I'm explaining you okay you can do ratio based you can do sticky sessions that
means if one request is going to pod one then all the requests of that specific user have to go to pod one only okay so
this is called sticky sessions they can use path based load balancing they can use domain or host based load balancing
okay they support uh you know whitelisting that means only allow
specific customers to access the application they can do blacklisting that means to say okay so these
customers are like hackers do not allow any users coming from Pakistan for example or do not allow any users coming
from USA do not allow any users coming from Russia so you can Define your traffic and you can say that okay so
this is the concept or these are the capabilities that Enterprise load balancers support
now the problem was when these people who were doing this virtual machines and
applications when they migrated from this to kubernetes okay so initially
they were very happy that kubernetes was offering you Auto healing Auto scaling automatic service Discovery uh exposing
the applications to external world so you know people used to create the same things that we we did like you know they
used to create a deployment after the deployment they used to create a service right and using the services they used
to get all the features that are available and using the deployment they used to get the healing and scaling
capabilities but off late they realized that okay service was providing load balancing mechanism but the load
balancing mechanism the service was providing is a simple round
Robin load balancing what is round robin if you are doing 10 requests what this
specific uh service using Q proxy right Cube proxy is updating your IP table rules what it does is it will send file
requests to pod number one and it will send five requests to pod number two let's assume there are two pods but this
is a very simple load balancing because people were coming from uh virtual machines and they used to get all of
these features against what they are getting in kubernetes is a very simple round robin they are not getting all of
these features and these are only list of features I gave you the commercial or the Enterprise load balancers they can
offer hundreds of features okay so you can simply read and you will see that you can do a web application firewall
you can do uh you know a lot of configurations like TLS you can add more
security using TLS so these load balancers offers all of these features okay so I within uh during this video
itself I have listed 10 close to 10 features which kubernetes was not supporting so these people were unhappy
with kubernetes okay so they said that okay a service was doing few things but still we are not happy and the other
thing that they have noticed this is problem number one and the problem number two is uh you can expose your
applications to external World using load balancer mode service right you can create your service as load balancer
mode but what is the problem is every time like let's say you have 100 Services if
you take companies like Amazon they have some thousands of services okay so for each of the service when they create the
service as type load balancer mode you know the cloud provider was charging
them for each and every IP address because these are Dynamic and public IP
address sorry sorry these are static public IP address so they don't charge
for the dynamic IP address but whenever the IP address becomes static so for static load balancing IP addresses and
static public load balancing IP address so if there are thousands of micro services or if there are thousands of
services that you require for your applications on kubernetes so cloud provider was charging very heavy
and Cloud providers are right in their terms because you are asking them for a static load balancing IP address and
they are charging you for money okay so this is another problem that these these people were facing in the previous
example okay what they used to do is because there was only one load balancer
okay in the contrary you have for each application you have one service right but on the physical or virtual virtual
servers people used to create one load balancer whether you have one application two application three applications so they used to configure
in their load balancer like okay if the request is coming to amazon.com
slash ABC send request to app one if it is coming to slash XYZ go to app2 and they
used to only expose this application uh sorry they only used to expose these load balancer with static public IP
address so what is happening is here they just
have one IP address which they are getting from the cloud provider or even within their organization they are only
exposing one specific IP address whereas here what is happening is you are exposing thousands of IP address and you
are getting charged so this is problem number two so let us write the two problems so that it is very clear to you
before I jump on to Ingress and how Ingress is solving this problem what is the problem number one
okay so the problem number one that we discussed is
Enterprise and
TLS that is secure load balancing capabilities so if you are using a service this thing
is missing people who are coming from the virtual machines they had very good load balancing capabilities like one two
three four five that I discussed in the previous slide like for example I can give you a basic example like it is
missing sticky sessions then it is missing uh TLS based load balancing that is secure load balancing https based
load balancing then the other thing it is missing was uh some uh a path based
load balancing like I just told you host based load balancing or domain-based load balancing so if request is going to
amazon.com go to this specific application if the request is going to amazon.in go to other applications so
that is host based load balancing and then there are many other things like uh like I told you ratio based load
balancing so I can write this list to 15 to 20 different things on top of my head but
you know it will only waste our time so what what is the thing is the services
in kubernetes was not offering all of this Enterprise level capabilities and the second point is
I just told you that if you are creating a service of type load balancer
then for each service kubernetes will charge you kubernetes will not charge you the cloud provider will actually
charge you right so the cloud provider will charge you this is a very important interview question as well people will
ask you what is the difference between load balancer type service and the uh traditional
kubernetes Ingress okay so what you will answer is the load balancing type service was good but it was missing all
of these capabilities and also you will say that the cloud provider will charge you for each and every load balancer
service type like if there are thousands of services you will be getting charged for thousands of load balancer static
public IP addresses okay so these are the two problems and these two problems you have to remember and it they have to
be on top of your head because this is very important interview Point okay so people will definitely ask you in your
uh interviews that what is ingress or why Ingress has to be created what is difference between load balancer service
type and Ingress so these questions will keep coming so definitely you have to remember those two points and now how
Ingress is solving those problem okay so what now kubernetes said is so kubernetes also admitted the problem
so kubernetes said that yeah we understand and till that point what happened was open shift open shift which
is red hat openshift which is again a kubernetes distribution they have implemented something called as
openshift routes which is very similar to kubernetes Ingress so kubernetes has understood that okay openshift has also
implemented something to solve the problem and even many users are requesting us saying that okay so this
is a very valid problem when we were on Virtual machines this is VM and this is kubernetes
okay so these customers kept on complaining on kubernetes GitHub page that when we were on Virtual machines we
were enjoying all the good capabilities of load balancers okay and because of which our
applications were very secure because of which you know we had reduced cost but once we moved to kubernetes we realized
that this is a very big problem so kubernetes people have also agreed to
it and what people at kubernetes said is okay we will Implement something called Ingress
okay so we will allow the users of kubernetes to create a resource called
Ingress and what you people have to do who are these people like nginx
F5 ambassador okay traffic
uh what are the other things like okay there are bunch of h a proxy so
these were the top load balancers that people were using uh here on the virtual
machines I don't think Ambassador was there till now but okay that doesn't matter so people were using these uh top
load balancers and what kubernetes said is okay I cannot Implement for each and every load balancer so what I'll do is I
will tell my users to create something called as a Ingress resource okay so as a kubernetes user you will create a user
sorry you will create a resource called Ingress resource and now all of these
load balancers okay so all of these companies what they will do is kubernetes set them that you create
something called as Ingress controller now what is this Ingress controller okay
so on a high level if you are creating Ingress resource on your kubernetes cluster and if you are saying that I
need a path based routing for example okay so you realize that you are missing
the path based routing on kubernetes which you are very heavily using on your virtual machines so you can come to your
kubernetes cluster create a Ingress resource I'll show you the example don't worry and you can say that okay I want
to create path based routing so you can kubernetes at that okay create one yaml file and inside the DML file say that
you know I want Pathways routing so you said the same thing but who will implement this okay so who will decide
that which load balancer you want to use so there are hundreds of load balancers in the market so what kubernetes said is
okay we cannot support all of you uh you know we cannot create the logic for all of you in the kubernetes master or the
API server instead you people create something called as Ingress controller okay what does English controller mean
so let's say that you want to create uh this specific capability using nginx
load balancer so the nginx company will write a nginx Ingress controller and as kubernetes users on this kubernetes
cluster you will deploy the Ingress controller okay you can deploy that using Helm charts you can deploy that
using yaml manifest okay and once you deploy the developer or again the devops
engineers they will create the Ingress ml service yaml resource for their kubernetes services okay so this Ingress
controller will watch for the Ingress resource and it will provide you the path based routing okay so if it is
complicated don't worry I'm explaining again okay so for example let's say this
is your kubernetes cluster what you are doing is you are creating a pod for example okay so you are writing
a yaml manifest for this and you have created a part now what will happen like I told you there is a component called
cubelet this cubelet will deploy your pod onto
one of the worker nodes so cubelet will also sit on the worker node and API server will notify cubelet using
scheduler that okay a pod is created and cubelet will deploy the pod
right and similarly let's say you are creating a service yaml manifest okay so there is
Q proxy and this Q proxy will what it will do this Q proxy will update the IP table
rules so for every resource that you are creating in kubernetes there is a component which is watching for that
resource and it is performing the required action okay so similarly even
if you are creating Ingress in kubernetes let's say you are creating Ingress
so there has to be a resource or component or a controller
which has to watch for this Ingress right so this was the problem so kubernetes said that okay I can create
Ingress resource but if I have to implement Logic for all the load balancers that are available in the market that is nginx F5 traffic
Ambassador HF proxy so kubernetes said that okay it is technically impossible I
cannot do it so what I'll do is I'll come up with the architecture and the architecture is user
will create Ingress resource okay
load balancing companies like nginx
F5 or any other load balancing companies they will write their own Ingress
controllers and they will place these Ingress controllers on GitHub and they will
provide the steps on how to install this Ingress controllers using Helm charts or any other ways and as a user instead of
just creating Ingress resource you also have to deploy Ingress controller okay
so it is up to the organization to choose which Ingress controller they
want to use what is ingress control at the end of the day it is just a load balancer
right sometimes it can be a load balancer plus API Gateway as well API Gateway offers you some additional
capabilities okay so end of the day what you need to do as
a user is on your kubernetes cluster the prerequisite is deploy a Ingress
controller which Ingress controller you will deploy let's say in your virtual machines world before you move to
kubernetes if you are using nginx so you will go to nginx GitHub page and
you will deploy the nginx Ingress controller onto the kubernetes cluster after that you will create Ingress
resource depending upon the capabilities that you need okay if you need path based routing you will create one type
of Ingress if you need TLS based Ingress you will create one type of Ingress if you need host base you will create one
type of Ingress so this is one time activity the one time activity for the devops engineers is to decide which
Ingress controller they want what what is ingress controller to decide which load balancer they want okay it can be
nginx it can be a file and they will go to their organizational GitHub page they will find the steps on how to deploy
this and once they realize how to deploy they will after that it can be one service two service 100 Services they
will only write the Ingress resource once they write the Ingress resource like you know Ingress does not have to
be one-to-one mapping okay you can create one Ingress and you know you can handle 100 of services as well using
paths you can say if path is a go to service one if path is B go to service two I'll show you that don't worry about
it but you understood the topic here right what was the problem why Ingress was introduced what is ingress
controller you understood all of these things so once you understand this concept it is very easy for you okay so
the major thing that you have to understand is the problem number one that Ingress is solving that is the
kubernetes services did not have Enterprise level load balancing capabilities and which is very very
important you will say that move to kubernetes because containers are very lightweight because all of blah blah
blah blah blah but without security without good load balancing capabilities
nobody will move to kubernetes and kubernetes has realized that so that's why they have introduced Ingress and the
problem number two was if you are creating a service of type load balancer mode
Cloud providers were charging you for each and every IP address okay so these were the two problems that Ingress was
solved you understood this thing and the next thing that you need to understand after this is okay how to install
Ingress like if you just followed the uh document till here or the presentation
tool here and to you will go to your kubernetes cluster you will find one Ingress example for the yaml file and
you will just create a Ingress resource what will happen nothing will happen because you don't have Ingress
controller on your cluster okay so if the Ingress controller is missing
then you might create one Ingress two Ingress hundred Ingress nothing will
happen because the Ingress is of no use without Ingress controller and what is ingress controller Ingress controller is
a load balancer that you can choose from the requirement okay if you want engine mixing nginx load balancer you can
create nginx Ingress controller if you want fi uh or big-ip load balancer you
can choose fi Ingress controller if you want uh HF proxy then you can create h a proxy Ingress controller okay once you
create this Ingress controllers once the Ingress resource is created on the cluster they will watch for the Ingress
resource and they will what they will do is basically they will provide you the required capability if you require path
based load balancing they will help you with path based load balancing if you require host base it will help you with host place so this is the theory part I
hope the thing is clear till here if it is not clear you have to watch the video one more time before we jump onto the
Practical because theory is very very important and your interview questions will be on Theory
perfect so now let me stop sharing here and let me jump on to my other screen
and show you how to install and how to configure okay so let me get my terminal
let me start sharing the screen perfect so this is my screen and in the
last class we learned to tell Services okay so what I'll do is let me check if I have the same state if I do Cube CTL
get pods uh yeah I have this deployments that we create these pods that we created in the last class and if I do
Cube CTL get service as well perfect the service is also available so this is the service and if you have followed my last
class I showed you how to create deployment.yaml and how to create service.aml as well and many people have
tried it I watched in the comment section so really appreciate you people for doing it perfect so now let us see
if I can access the service on the first fall okay so I created this service as node Port service and this is the port
so what I'll do is firstly I'll get the mini Cube IP address so this is the mini Cube IP address so I can just use the
curl command perfect so I am getting the output learn devops with some strong foundational
knowledge great so we are able to verify that our
service is running our service is watching the pods and the application is running now let me create a Ingress
resource for this and what we will do with Ingress is we will set up a host based load balancing okay so what is
host based load balancing like I told you uh host based load
balancing is nothing but we can say that okay in the previous example we try to access using a curl Command right so
instead of this curl command I can say that allow users when they try to reach
my uh specific service on example.com okay or you can say hello if they try to reach on example.com ABC okay so let us
try to see how to create this so again there is no rocket science what I'll do is I'll simply go to the kubernetes
official documentation so uh I'll just say kubernetes Ingress
perfect so this is the official page for kubernetes Ingress and what I'll do is I'll just go here okay see the example
also here what these people are saying is there is ingress managed load balancer that is ingress controller okay
so you create an Ingress resource and using this Ingress controller you know you can Define how to route the traffic
for your applications or how to route the traffic for your pods I'll also paste the link in the description of the
previous video I I think I did the two to three months back that is a very very informative video because we spent
almost more than one hour explaining you different types of Ingress resources how to create them how to create a path
based load balancing how to do uh you know SSL offloading how to do pass through so lot of things in very detail
after watching this video if you have time definitely watch that video as well okay perfect so here I'll just copy this
example instead of this let me go for host okay so this is an example which has host go through all the examples uh
you can just follow the documentation and you can go through all the examples as well so I will do Ingress Dot yaml
okay and what I will do is I'll just modify these fields so instead of Ingress wildcard host I'll just say
Ingress example so let let us keep the same food.bar.com
no problem with it right instead of example.com let us just keep it as food.bar.com and now I'll say if anybody
wants to reach my application my service okay so if they hit on food.word.com
slash bar okay so they should reach the service one and they should you know
typically you have to provide what is your service name and what is your service port so let us see what is my
service name Cube CTL get SVC so this is my service name right
so let me go to my Ingress yaml and let me just replace the service name my port
number is 18 so I don't have any problem there perfect so now let us deploy this file
Cube CDL apply minus F Ingress dot ml and let me see if something is happening
okay so the Ingress is created if I do Cube CTL get Ingress you will notice
that the Ingress is created but the address field is empty and you will see that nothing will happen like even if I
try to replace this curl command and instead of this what I'll do is I'll just say
food.bar.com so this is domain based routing okay so
what we are achieving here is domain based routing and I will say food.bar.com
for example what was the path that we have provided slash bar okay and if I
try to hit nothing will happen here okay so the reason here nothing is happening is because you haven't created Ingress
controller right so only if you create the Ingress controller then your uh this
thing will start working right the Ingress should be read by Ingress controller so what we need to do is
firstly install the Ingress controller so let me install nginx Ingress controller because nginx is a quite
popular one so again I'll just follow the kubernetes documentation and say kubernetes Ingress install and this is
the example documentation where I can search for Ingress controller here there
are bunch of Ingress controllers that kubernetes supports like I explained you uh there is nothing like kubernetes
supports actually as load balancing company is they can create the Ingress controllers okay so all of these
companies have implemented their Ingress controllers like nginx has their own you know Ingress controller ha proxy as
their own fi has their own okay so Apache has their own Ingress controller like you can also create your own
Ingress controller if you have a load balancer perfect so let us see nginx Ingress
controller because it's a very lightweight and simple Ingress controller let us see how to install
so if you are trying to install on minicube like they provide you some very easy steps uh where is this
nginx Ingress controller works with kubernetes perfect so you can just say try nginx Ingress
controller okay here the steps are not good I can just say kubernetes
nginx Ingress controller mini Cube because I am installing on
minicube so you know there is a very good documentation straight forward like you can use this same example that I'm
showing you just search for kubernetes engine X Ingress controller mini Cube and this is one simple command that will
install nginx Ingress controller on your minicube cluster so all that you need to do is mini Cube enable add-ons sorry
add-ons enable Ingress and that will create a Ingress controller for you right so it's a very simple step that
you have to do uh additionally like if you want to know how to deploy Ingress controller for your production like you
know in production you will not use mini Cube probably you are using uh eks clusters or openshift or some some
things right so in such gate go back to the documentation that I showed you just search for kubernetes Ingress okay go
back here and choose which Ingress controller you want let us say that you are doing this in your organization so
the steps you will follow is go to this Ingress controller and let's say you are using same engine Ingress controller
okay instead of choosing the page this page where you will only be able to install for mini Cube okay go back here
and in the documentation what you need to do so these are individual documentations right for every Ingress
controller you have their own documentation so you can come here and you can provide you can look for the
steps to install uh where is this
uh installation steps
let us search for install directly
okay let us choose a different Ingress controller I think they have complicated the steps here let me just search for
Ambassador okay so I took this randomly and here once you click on a quick start
so again this is asking for some okay don't worry uh so they are just asking
for all the sign up and all the things but uh you can just search for their uh
official product documentation and say for example Ambassador ambassador Ingress
controller installation okay so just search in Google like this and you will directly find the steps for installing
Ambassador uh Ingress controller or you know anything that is required for your organization so if you are doing on Mini
Cube you don't have to worry about anything like you know this is the official documentation for Ambassador installing uh Ambassador Ingress
controller okay so you have the direct steps here install with Helm install with Cube City ml so on your production
cluster you can choose probably Helm so click on the helm instructions and you can install them using these specific
commands but for mini Cube like I told you we just have to do mini Cube add-ons enable Ingress and it will install the
Ingress controller for us so let us see if the Ingress controller is installed or not end of the day English controller
is also a pod so Cube CDL get pods minus a because I am not sure in which
namespaces that is installed and just say nginx so see here so nginx Ingress controller
is up and running and in which namespace it has created its own namespace coil Ingress nginx okay and let us try to see
the logs and let us see if it has identified the Ingress resource that we have created Cube CTL logs minus n
uh what was the namespace Ingress hyphen engine X okay so click on enter and it
should identify the Ingress resource that we have created so what was the Ingress resource that we have created
see here Ingress example we have created in the default namespace and the name was Ingress example okay so it said that
it has identified that we have created Ingress resource and it has successfully synced as well so what does it mean like
it synced so it will go to the nginx load balancer configuration that is
nginx.com file okay and it will update some Ingress related configuration for
our load balancer related configuration for the English resource that we created and you don't have to go into these
details at this point of time so with using the Ingress controllers you will understand like you know don't learn
everything on day one eventually you will understand what is happening under the hood but as I showed you in the Pod
logs it has unidentified that you know Abhishek has created an Ingress called
example Ingress or Ingress example in the default namespace right this is a default namespace and it said that the
configuration is synced okay for example tomorrow you are getting any error what you need to do is you have to go back
and see in the pod.logs and now if you notice this address field was not there
previously but it is updated now okay so if I can show you that I don't know if my terminal okay so the logs are deleted
but address field was not there previously but after creating the Ingress controller this address field is
populated that means to say nerve I can access says you know I can use the
Ingress resource on food.bar.com what was the Ingress resource that we
have created this Ingress example now can be accessed on food.bar.com bar the
reason why I can access is because I have used the Ingress controller right
and the Ingress controller has updated the configuration so in your production
environment this is enough but if you are trying on your local kubernetes cluster you have to do one more
configuration that is you have to update the slash Etc host configuration okay
you have to update this file why you need to update this file and why you don't need to update this in production
is because because you are doing on local and you have not done this domain mapping so this food.bar.com has to be
mapped with the domain or IP address that is 192. 168 64.11 so this is my
mini Cube or you know this is my Ingress IP address not mini Cube this is my Ingress IP address so whenever I try to
say food.bot.com like if you ping and say food.bar.com this IP address does not exist right this domain does not
exist in your real-time production environment for example for people at Amazon they might be using amazon.com
and amazon.com is a real domain which does exist so in their Ingress resource what they will do is they will simply
mention here as amazon.com but because we have not a company and we are just doing a domain uh sorry demo video so I
cannot go to GoDaddy and purchase a domain right so that's why I simply said food.bar.com but what you can do is you
can mock this Behavior or you know you can create a dummy Behavior here like you can confuse your uh laptop or you
can you know you can update the ETC host file like sudo
update this host file and tell the host file that let's just provide the
password so you can just tell it right I know this IP address called
what was the IP address uh uh sorry I have to go back so you can tell
them that okay if somebody is trying to reach food or bar.com okay you can tell
them that I know this domain called fuba.bar.com and just provide this IP address and tell them that or tell the
machine that foo.bar.com okay so this IP address
sorry this domain will be resolved on this specific IP address okay so now if
you try to access food.bar.com it will try to reach 192.168.64.11 so this way you can mimic
the behavior or mock the behavior but this is not production use case in production you don't have to do all of these things you can simply ask your
manager or you can simply ask your company what is the domain that we use and you can provide the domain name now
if I try to Pink food.bar.com okay so you will notice that okay so the
request is not reaching yet but in some time you will notice that the request will reach onto food.bar.com okay so did
I do any mistake here no there is no mistake so okay uh there are some previous entries I have to delete this
entries okay uh right perfect so in some time what you
will notice is when somebody tries to reach on the specific food.bar.com you
can tell through your curl request or you can tell through uh you know slash Etc host even if you don't want to
update the ctcos you can also tell that in your curl command that okay I know what is uh food.bar.com you can resolve
if somebody tries to access food or bar.com just resolve this on IP address 192 168 64.11.
okay so now what will happen after some time is you will be able to reach the application uh just replace the curl
command with uber.paul.com and your application will be reached now okay I
can go into that practicals but before that you need to understand that go through this document where you will
find multiple other things like this was just example of uh host and Pathways routing right similarly you can do TLS
based routing so what is TLS just search for TLS here and you will see that you can create uh secure kubernetes
ingresses as well that means to say that you know uh this Ingress resource that I have created anybody can accept
access access on the HTTP request but in production real-time use cases for example you are accessing google.com you
will access on https so these all things can also be done using Ingress and if
you want to do this if you want to try the Practical okay you can follow the video that I am pasting in the
description okay it has everything like I have shown all the types of Ingress that were available I think it was made
two to three months back I have shown all the type of Ingress as TLS without TLS host based path based wildcard entry
so follow that video after this so that you understand the entire concept okay so if you like the button click on the
like button if you have understood the concept of Ingress definitely comment on the video and I'll see in the next video
take care everyone bye
