**Kubernetes Namespaces & Services -- Day 10 Cheat Sheet**
=========================================================

> **Goal:** Understand namespaces, how resources are isolated, and how services are resolved across namespaces using DNS.

* * * * *

**1\. Shell & Environment Setup**
---------------------------------

```
vi ~/.bashrc        # Edit shell aliases (like k=kubectl, kgp, kgn)
source ~/.bashrc    # Reload bash configuration
```

```
pwd                 # Check current directory
cd cka              # Move into CKA practice folder
cd Day-10           # Enter Day-10 directory
ls                  # List files
clear               # Clear terminal
```

* * * * *

**2\. KIND Cluster Creation**
-----------------------------

### **kind.yaml**

```
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 30001
    hostPort: 30001
- role: worker
- role: worker
```

### **Create cluster**

```
kind create cluster --name cka-cluster1 --config kind.yaml
```

### **Verify nodes**

```
kgn   # kubectl get nodes
```

* * * * *

**3\. Namespaces Basics**
-------------------------

### **List namespaces**

```
k get namespaces
```

Default namespaces:

-   default

-   kube-system

-   kube-public

-   kube-node-lease

-   local-path-storage

### **Get all resources in kube-system**

```
k get all -n kube-system
```

* * * * *

**4\. Create & Manage Namespace**
---------------------------------

### **Using YAML**

```
k apply -f namespace.yaml
```

### **Using command**

```
k create ns demo
```

### **Delete namespace**

```
k delete ns demo
```

* * * * *

**5\. Deployments in demo Namespace**
-------------------------------------

### **Create deployment**

```
k create deploy nginx-demo --image nginx -n demo
```

### **List pods in demo**

```
kgp -n demo
kgp -n demo -o wide
```

### **Exec into pod (common mistake fixed)**

```
k exec -it <pod-name> -n demo -- sh
```

* * * * *

**6\. Scaling Deployments**
---------------------------

```
k scale --replicas=3 deploy/nginx-demo -n demo
```

Verify:

```
kgp -n demo
```

* * * * *

**7\. Services in demo Namespace**
----------------------------------

### **Expose deployment**

```
k expose deploy/nginx-demo --name=svc-demo --port=80 -n demo
```

### **List services**

```
k get svc -n demo
```

Service type: ClusterIP

* * * * *

**8\. DNS & Service Discovery (demo namespace)**
------------------------------------------------

Inside pod:

```
cat /etc/resolv.conf
```

Search domains:

-   demo.svc.cluster.local

-   svc.cluster.local

-   cluster.local

### **Curl service**

```
curl svc-demo
curl svc-demo.demo.svc.cluster.local
```

* * * * *

**9\. Default Namespace Comparison**
------------------------------------

### **Create deployment**

```
k create deploy nginx-test --image nginx
```

### **Scale**

```
k scale --replicas=3 deploy/nginx-test
```

### **Expose service**

```
k expose deploy/nginx-test --name=svc-test --port=80
```

* * * * *

**10\. Cross-Namespace Communication**
--------------------------------------

### **From default → demo**

```
curl svc-demo.demo.svc.cluster.local
```

### **From demo → default**

```
curl svc-test.default.svc.cluster.local
```

❌ This will NOT work:

```
curl svc-demo
```

✅ Reason: Services are namespace-scoped.

* * * * *

**11\. Key Takeaways (CKA Gold)**
---------------------------------

-   Namespaces isolate **pods, services, deployments**

-   DNS format:

```
<service>.<namespace>.svc.cluster.local
```

-   Same-namespace → short name works

-   Cross-namespace → FQDN required

-   kubectl exec  **must include namespace**

* * * * *

**12\. Common Mistakes You Hit (and fixed)**
--------------------------------------------

-   ❌ k exec --it → ✅ k exec -it

-   ❌ Missing -n demo

-   ❌ Expecting service to resolve across namespaces automatically

* * * * *

**13\. Exam Tips 🧠**
---------------------

-   Always check namespace first (kgp -A)

-   Use -n aggressively

-   DNS questions are **very common** in CKA

* * * * *

✅ This lab perfectly covers:

-   Namespaces

-   Services

-   DNS

-   Pod-to-pod communication

You're doing solid CKA-level work here 💪**CKA Cheat Sheet --- InitContainers, Services & DNS (DAY-11)**
=============================================================

> **Goal:** Use an InitContainer to block app startup until a Service becomes reachable via DNS.

* * * * *

**1️⃣ Pod with InitContainer (multi-pod.yaml)**
-----------------------------------------------

**Key ideas**

-   InitContainers run **before** app containers

-   Pod stays in Init:*/* until init completes

-   Common use: wait for DB / Service readiness

```
apiVersion: v1
kind: Pod
metadata:
  name: myapp
  labels:
    env: dev
spec:
  initContainers:
  - name: myinit-svc   # must be lowercase RFC1123
    image: busybox:1.28
    command: ['sh', '-c']
    args:
    - |
      until nslookup myservice.default.svc.cluster.local; do
        echo waiting for service to be up
        sleep 2
      done
      echo Service is up!

  containers:
  - name: myapp
    image: busybox:1.28
    command: ['sh', '-c', 'echo Hello from Myapp container && sleep 3600']
    env:
    - name: FIRST_NAME
      value: "John"
```

Apply:

```
kubectl apply -f multi-pod.yaml
```

* * * * *

**2️⃣ Observing InitContainer Behavior**
----------------------------------------

```
kubectl get pods
# STATUS: Init:0/1

kubectl logs myapp -c myinit-svc
# nslookup fails → keeps waiting
```

👉 **Expected**: Pod stuck in Init until service exists

* * * * *

**3️⃣ Create Backend Deployment + Service**
-------------------------------------------

```
kubectl create deploy nginx-deploy --image=nginx
kubectl expose deploy nginx-deploy --name=myservice --port=80
```

Verify:

```
kubectl get svc myservice
```

* * * * *

**4️⃣ InitContainer Completes → App Starts**
--------------------------------------------

```
kubectl describe pod myapp
```

**Key signs**

-   InitContainer: Completed

-   Pod status: Running

Logs:

```
kubectl logs myapp -c myinit-svc
# Service is up!

kubectl logs myapp
# Hello from Myapp container
```

* * * * *

**5️⃣ DNS & Environment Variable Injection**
--------------------------------------------

```
kubectl exec -it myapp -- printenv
```

**Auto-injected by Service**

```
MYSERVICE_SERVICE_HOST=10.96.24.79
MYSERVICE_SERVICE_PORT=80
MYSERVICE_PORT=tcp://10.96.24.79:80
```

👉 Services create **DNS + ENV vars** automatically

* * * * *

**6️⃣ Access Environment Variables**
------------------------------------

```
kubectl exec -it myapp -- sh

# echo $FIRST_NAME
John
```

* * * * *

**7️⃣ Recreate Pod After Deletion**
-----------------------------------

```
kubectl delete pod myapp
kubectl apply -f multi-pod.yaml
```

✔ InitContainer runs again

* * * * *

**8️⃣ Multiple Services Dependency (Advanced)**
-----------------------------------------------

Create another backend:

```
kubectl create deploy mydb --image=redis
kubectl expose deploy mydb --name=mydb --port=80
```

Services:

```
kubectl get svc
```

* * * * *

**9️⃣ Exam Gotchas 🚨**
-----------------------

❌ InitContainer name must be lowercase

❌ Pod logs default to app container

❌ Service DNS format is mandatory

✅ Correct FQDN:

```
<service>.<namespace>.svc.cluster.local
```

* * * * *

**🔟 One-Line Exam Summary**
----------------------------

> **InitContainers block Pod startup until dependencies (DNS/Service/DB) are ready.**

* * * * *

**🧠 CKA Memory Hook**
----------------------

-   Init:*/* → init running

-   Completed → app starts

-   Service → DNS + ENV vars

-   Always test with nslookup

* * * * *

✅ **High-probability CKA topic**