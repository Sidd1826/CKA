**InitContainer + Service DNS Troubleshooting (CKA Cheat Sheet)**
=================================================================

This cheat sheet walks through a **real-world initContainer issue** where a Pod gets stuck in Init:0/1 because a Service DNS name never resolves.

* * * * *

**1\. Problem Statement**
-------------------------

Pod status:

```
Init:0/1
PodInitializing
```

Init container logs:

```
nslookup: can't resolve 'mysvc.default.svc.cluster.local'
waiting for service to be up
```

* * * * *

**2\. Root Cause**
------------------

### **❌ Service name mismatch**

Init container is checking:

```
mysvc.default.svc.cluster.local
```

But the actual Service created:

```
myinit-svc
```

So DNS **never resolves**, and the init container **never exits**.

* * * * *

**3\. Key Kubernetes Concepts**
-------------------------------

### **Init Containers**

-   Run **before** main containers

-   Must **complete successfully**

-   If they fail or hang → Pod never starts

* * * * *

### **Service DNS Format**

```
<service-name>.<namespace>.svc.cluster.local
```

Example:

```
myinit-svc.default.svc.cluster.local
```

* * * * *

**4\. Common Mistakes (Seen Here)**
-----------------------------------

### **1️⃣ Typo in image name**

```
bussybox ❌
busybox  ✅
```

Leads to:

```
ErrImagePull
ImagePullBackOff
```

* * * * *

### **2️⃣ Confusing Pod vs Deployment**

|

**Resource**

 |

**Command**

 |
| --- | --- |
|

Pod

 |

kubectl expose pod <pod-name>

 |
|

Deployment

 |

kubectl expose deploy <deploy-name>

 |

Pods **cannot** be exposed using deploy.

* * * * *

### **3️⃣ Exposing Pod name instead of Deployment name**

```
task-nginx-67b8698c4-h5qql ❌ (Pod)
task-nginx                ✅ (Deployment)
```

* * * * *

**5\. Correct Pod YAML (Working)**
----------------------------------

```
apiVersion: v1
kind: Pod
metadata:
  name: task-apps
  labels:
    env: dev
spec:
  initContainers:
  - name: myinit-svc
    image: busybox:1.28
    command:
    - sh
    - -c
    args:
    - |
      until nslookup myinit-svc.default.svc.cluster.local; do
        echo "waiting for service to be up"
        sleep 2
      done
      echo "Service is up!"

  containers:
  - name: task-apps
    image: busybox:1.28
    command:
    - sh
    - -c
    - echo "Hello from Task app container" && sleep 3600
    env:
    - name: FIRST_NAME
      value: "Sidd"
```

* * * * *

**6\. Apply & Verify**
----------------------

```
kubectl delete pod task-apps
kubectl apply -f task.yaml
```

Watch:

```
kubectl get pods -w
```

Expected:

```
Init:0/1 → Init:Completed → Running
```

* * * * *

**7\. Verification Commands**
-----------------------------

Init container logs:

```
kubectl logs task-apps -c myinit-svc
```

Main container logs:

```
kubectl logs task-apps
```

* * * * *

**8\. CKA Exam Tips**
---------------------

-   Init containers are **blocking**

-   DNS issues often mean **Service name mismatch**

-   Always check:

    -   kubectl describe pod

    -   Events section

    -   Init container logs

* * * * *

**9\. One-Line Diagnosis (Exam Ready)**
---------------------------------------

> Pod stuck in Init:0/1 because initContainer is waiting on a Service DNS name that does not exist.

* * * * *

✅ **This scenario covers:**

-   InitContainers

-   Service DNS

-   Pod vs Deployment

-   ImagePull errors

-   Real CKA-style troubleshooting

Save this. Revise it. You *will* see this pattern in the exam.