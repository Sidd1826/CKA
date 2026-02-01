# Kubernetes Workloads – ReplicationController, ReplicaSet & Deployment (CKA Notes)

---

## Shell & Alias Validation
- `source ~/.bashrc`  
  → Reloads shell configuration to restore kubectl aliases.

- `kubectl get pods`  
  → Lists all pods in the default namespace.

---

## Cleaning Existing Pods
- `kubectl delete pod nginx`  
  → Deletes nginx pod.
- `kubectl delete pod nginx-task`  
  → Deletes nginx-task pod.
- `kubectl get pods`  
  → Confirms no pods exist.

---

## Node Verification
- `kubectl get nodes`  
  → Confirms control-plane and worker nodes are Ready.

---

## Navigating to Manifests
- `pwd`  
  → Displays current directory.
- `cd cka`
- `cd Day-8/`
- `ls`  
  → Lists workload YAML files.

---

## ReplicationController Creation
- `kubectl apply -f replicationController.yaml`  
  → ❌ Fails due to typo `spec.template.metadat`.
- `kubectl apply -f replicationController.yaml`  
  → ✅ ReplicationController created successfully.

- `kubectl get pods`  
  → Shows 3 pods created by ReplicationController.
- `kubectl get replicationcontroller`  
  → Confirms desired = current replicas.

---

## ReplicaSet Creation Errors
- `kubectl apply -f test.yaml`  
  → ❌ Fails: wrong API version (`v1` used instead of `apps/v1`).
- `kubectl apply -f test.yaml`  
  → ❌ Fails: invalid field `spec.template.selector`.
- `kubectl apply -f test.yaml`  
  → ✅ ReplicaSet created successfully.

---

## Image Pull Failures
- `kubectl get pods`  
  → Pods show `ErrImagePull` / `ImagePullBackOff`.

- `kubectl describe pod nginx-rc-25hw9`  
  → Shows invalid image name `nginx-latest`.

### Root Cause
- Image `nginx-latest` does not exist on Docker Hub.
- Correct image should be `nginx` or `nginx:latest`.

---

## Deleting ReplicaSet (Correct Way)
- `kubectl delete rs/nginx-rs`  
  → Deletes ReplicaSet and its pods.

---

## Cleaning ReplicationController
- `kubectl delete rc/nginx-rc`  
  → Deletes ReplicationController and its pods.

---

## Recreating ReplicaSet Successfully
- `kubectl apply -f test.yaml`  
  → ReplicaSet created.
- `kubectl get pods`  
  → All ReplicaSet pods are Running.

---

## Scaling ReplicaSet
- `kubectl scale --replicas=5 rs/nginx-rs`  
  → Scales ReplicaSet to 5 pods.

- `kubectl get pods`  
  → Confirms scaling.

---

## Editing ReplicaSet Live
- `kubectl edit rs/nginx-rs`  
  → Opens live ReplicaSet configuration for editing.

---

## Cleanup Before Deployment
- `kubectl delete rc/nginx-rc`
- `kubectl delete rs/nginx-rs`
- `kubectl get pods`  
  → Confirms empty namespace.

---

## Deployment Creation
- `kubectl apply -f test.yaml`  
  → Creates Deployment `nginx-deploy`.

- `kubectl get pods`  
  → Multiple pods created via Deployment.

---

## Deployment Verification
- `kubectl get all`  
  → Shows Pods, ReplicaSet, Deployment hierarchy.

---

## Updating Deployment
- `kubectl apply -f test.yaml`  
  → Updates Deployment configuration.

- `kubectl describe deployment nginx-deploy`  
  → Shows rolling update behavior:
  - Old ReplicaSet
  - New ReplicaSet
  - Surge and unavailable pods

---

## Rollout History
- `kubectl rollout history deployment nginx-deploy`  
  → Displays revision history.

---

## Rollback Deployment
- `kubectl rollout undo deployment nginx-deploy --to-revision=1`  
  → Rolls back Deployment to revision 1.

- `kubectl rollout history deployment nginx-deploy`  
  → Confirms rollback revision.

- `kubectl describe deployment nginx-deploy`  
  → Verifies image reverted successfully.

---

## Dry Run Deployment YAML
- `kubectl create deployment nginx-new --image=nginx --dry-run=client -o yaml > deploytest.yaml`  
  → Generates Deployment YAML without creating resource.

---

## Key CKA Takeaways
- **Never use ReplicationController in new designs** (exam still tests it).
- ReplicaSet requires:
  - `apps/v1`
  - Mandatory `selector`
- Deployment manages ReplicaSets automatically.
- ImagePullBackOff = wrong image name (90% exam cases).
- Use `kubectl describe` for root-cause analysis.
- Deployment rollback is instant and exam-friendly.
- Always prefer `--dry-run=client -o yaml` to generate manifests.

---