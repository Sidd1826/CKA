# Kubernetes Pods – Tasks & Commands (CKA Notes)

## Shell & Alias Issues
- `kubectl get pods`  
  → Command fails because alias is not loaded.
- `source ~/.bash`  
  → Fails because `.bash` file does not exist.
- `source ~/.bashrc`  
  → Reloads bash configuration and restores aliases like `k`, `kgp`.

---

## Listing Existing Pods
- `kubectl get pods`  
  → Lists running pods in the default namespace.

---

## Deleting Existing Pods
- `kubectl delete pod nginx-pod-dec`  
  → Deletes the declaratively created nginx pod.
- `kubectl delete pod nginx-pod-im`  
  → Deletes the imperatively created nginx pod.
- `clear`  
  → Clears terminal screen.

---

## Creating Pod Imperatively
- `kubectl run nginx --image nginx`  
  → Creates a pod named `nginx` using the nginx image.
- `kubectl get pods`  
  → Confirms pod is running.

---

## Exporting Pod YAML
- `kubectl get nginx -o yaml`  
  → ❌ Fails because `nginx` is not a resource type.
- `kubectl get pod nginx -o yaml > task_pod-imp.yaml`  
  → Exports running pod definition to a YAML file.

---

## Creating Pod from Modified YAML
- `vi task_pod-imp.yaml`  
  → Opens YAML file for editing.
- `kubectl apply -f task_pod-imp.yaml`  
  → Creates a new pod named `nginx-task`.
- `kubectl get pods`  
  → Shows both `nginx` and `nginx-task` running.

---

## Creating Redis Pod Declaratively
- `vi task3_pod.yaml`  
  → Opens Redis pod definition file.
- `kubectl apply -f task3_pod.yaml`  
  → Creates a Redis pod.
- `kubectl get pods`  
  → Redis pod initially shows `ContainerCreating`.

---

## Debugging Redis Pod
- `kubectl describe pod redis`  
  → Displays detailed pod information:
  - Image name (`redis`)
  - Scheduling node
  - Container state (`ContainerCreating`)
  - Events showing image pull in progress

---

## Key Observations from Describe Output
- Pod is scheduled successfully.
- Image pull has started.
- No errors present → normal behavior during image download.
- Pod will move to `Running` once image pull completes.

---

## Key CKA Takeaways
- Always `source ~/.bashrc` if aliases stop working.
- Resource type must be specified (`kubectl get pod`, not `kubectl get nginx`).
- Exporting live pod YAML is useful for:
  - Re-creating pods
  - Exam tasks
- `kubectl describe pod` is the **first command** for troubleshooting.
- `ContainerCreating` usually means:
  - Image pull in progress
  - Volume setup in progress

---
