**Day 9 -- Kubernetes Services (Hands-on Command Breakdown) ☸️**
===============================================================

This document explains **each command used in the NodePort, ClusterIP, and LoadBalancer demo**, in **one-liners**, exactly in the order executed.

* * * * *

**Environment Setup**
---------------------

```
source ~/.bashrc
```

➡ Loads shell aliases and environment variables (e.g., k, kgp).

```
kgp
```

➡ Lists all Pods in the current namespace (kubectl get pods).

```
pwd
```

➡ Shows the current working directory.

```
cd cka
cd Day-9
pwd
```

➡ Navigates to the Day-9 Kubernetes practice directory.

* * * * *

**Creating a Kind Cluster**
---------------------------

```
kind create cluster --name svc-cluster --config kind.yaml
```

➡ Creates a new Kind Kubernetes cluster with port mappings defined in kind.yaml.

```
kgn
```

➡ Lists all nodes in the cluster (kubectl get nodes).

```
k config get-contexts
```

➡ Shows all kubeconfig contexts and highlights the active one.

* * * * *

**Deploying NGINX Application**
-------------------------------

```
cat ../Day-8/deploy.yaml
```

➡ Displays the Deployment YAML for the NGINX application.

```
k apply -f ../Day-8/deploy.yaml
```

➡ Creates the NGINX Deployment with 3 replicas.

```
kgp
```

➡ Verifies Pods are being created for the Deployment.

* * * * *

**Creating NodePort Service**
-----------------------------

```
k apply -f NodePort.yaml
```

➡ Attempts to create a NodePort Service.

```
Invalid value: "nodeport-Service"
```

➡ Service name is invalid because Kubernetes requires **lowercase DNS-1035 names**.

```
k apply -f NodePort.yaml
```

➡ Successfully creates the NodePort Service after fixing the name.

```
k get svc
```

➡ Lists all Services and shows NodePort mapped to port 30001.

* * * * *

**Verifying Pod and Service Mapping**
-------------------------------------

```
kgp
```

➡ Confirms all NGINX Pods are running.

```
k describe pod <pod-name>
```

➡ Displays detailed Pod info including IP, node placement, and container status.

* * * * *

**Accessing Application via NodePort**
--------------------------------------

```
curl localhost:30001
```

➡ Accesses the NGINX application through the NodePort exposed on the host.

* * * * *

**Creating ClusterIP Service**
------------------------------

```
cat ClusterIP.yaml
```

➡ Displays ClusterIP Service configuration.

```
k apply -f ClusterIP.yaml
```

➡ Creates an internal-only ClusterIP Service.

```
k get svc
```

➡ Confirms ClusterIP Service creation.

* * * * *

**Inspecting Service Endpoints**
--------------------------------

```
k get endpoints
```

➡ Shows the Pod IPs backing each Service (legacy API).

> ⚠️ Note: Endpoints is deprecated; Kubernetes now uses **EndpointSlice**.

* * * * *

**Creating LoadBalancer Service**
---------------------------------

```
cat LoadBalancer.yaml
```

➡ Displays LoadBalancer Service configuration.

```
k apply -f LoadBalancer.yaml
```

➡ Creates a LoadBalancer Service.

```
k get svc
```

➡ Shows LoadBalancer Service with EXTERNAL-IP in pending state (expected in Kind).

* * * * *

**Key Observations (CKA Important)**
------------------------------------

-   NodePort exposes app on <NodeIP>:<NodePort>

-   ClusterIP is accessible **only inside the cluster**

-   LoadBalancer behaves like NodePort in **local clusters** (Kind/Minikube)

-   Services route traffic using **labels and selectors**

-   curl localhost:<nodePort> works due to Kind port mapping

* * * * *

**Summary Flow**
----------------

```
Client → NodePort → Service → Pod
Client → ClusterIP → Service → Pod (internal)
Client → LoadBalancer → NodePort → Service → Pod (Kind)
```

* * * * *

✅ This completes the **Day 9 Kubernetes Services hands-on walkthrough**