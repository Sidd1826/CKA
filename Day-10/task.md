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

You're doing solid CKA-level work here 💪