**Kubernetes InitContainer & Service Debugging -- Cheatsheet**
=============================================================

This cheatsheet summarizes the **exact commands, patterns, and fixes** you used while debugging an InitContainer waiting on a Service (CKA-style 💪).

* * * * *

**Shell & Navigation**
----------------------

```
source ~/.bashrc
pwd
cd CKA/DAY-11
ls
cat multi-pod.yaml
```

* * * * *

**Apply & Inspect Resources**
-----------------------------

```
kubectl apply -f multi-pod.yaml
kubectl get pods
kubectl describe pod myapp
```

* * * * *

**Logs (Normal vs InitContainer)**
----------------------------------

```
kubectl logs myapp
kubectl logs myapp -c myinit-svc
```

-   -c → specify **container name** (needed when Pod has initContainers)

* * * * *

**Common Pod States (Observed)**
--------------------------------

-   Init:0/1 → InitContainer running

-   PodInitializing → Main container waiting

-   Running → All initContainers completed successfully

* * * * *

**Create Deployment**
---------------------

```
kubectl create deploy nginx-deploy --image nginx
kubectl create deploy mydb --image redis --port 80
```

* * * * *

**Expose Deployment as Service (ClusterIP)**
--------------------------------------------

```
kubectl expose deploy nginx-deploy --name myservice --port 80
kubectl expose deploy mydb --name mydb --port 80
```

Verify:

```
kubectl get svc
```

* * * * *

**DNS Debugging Inside InitContainer**
--------------------------------------

```
nslookup myservice.default.svc.cluster.local
```

Correct FQDN format:

```
<service>.<namespace>.svc.cluster.local
```

* * * * *

**Exec Into Running Pod**
-------------------------

```
kubectl exec -it myapp -- sh
```

Run commands inside container:

```
echo $FIRST_NAME
printenv
exit
```

* * * * *

**Environment Variables Injected by Service**
---------------------------------------------

Automatically available once Service exists:

```
MYSERVICE_SERVICE_HOST
MYSERVICE_SERVICE_PORT
MYSERVICE_PORT_80_TCP
```

* * * * *

**Delete & Recreate Pod**
-------------------------

```
kubectl delete pod myapp
kubectl apply -f multi-pod.yaml
```

* * * * *

**Typical InitContainer Pattern (Reference)**
---------------------------------------------

```
initContainers:
- name: wait-for-service
  image: busybox
  command: ['sh', '-c']
  args:
  - until nslookup myservice.default.svc.cluster.local; do sleep 2; done
```

* * * * *

**Key Learnings (CKA Gold ⭐)**
------------------------------

-   InitContainers **block** main containers until completion

-   DNS works **only after Service is created**

-   Always use kubectl logs -c for initContainers

-   Service env vars are injected **at Pod startup**

-   Recreate Pod if Service was created later

* * * * *

**Useful Aliases (Optional)**◊
-----------------------------

```
alias k=kubectl
alias kgp='kubectl get pods'
```

* * * * *

Happy CKA grinding 🚀◊