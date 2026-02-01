# Kubernetes Pods – Commands & Troubleshooting (CKA Notes)

## Shell Setup
- `source ~/.bashrc`  
  → Reloads shell configuration to enable kubectl aliases like `k`, `kgp`.

---

## Checking Existing Pods
- `kubectl get pods`  
  → Lists pods in the default namespace.
- Output: `No resources found in default namespace.`  
  → No pods are currently running.

---

## Creating Pods Imperatively
- `kubectl run nginx-pod-im --image nginx:latest`  
  → Creates a pod named `nginx-pod-im` using the nginx image.
- `kubectl get pods`  
  → Checks pod status (initially `ContainerCreating`).

---

## Navigation & File System
- `pwd`  
  → Shows current working directory.
- `cd cka`  
  → Navigates to the `cka` directory.
- `cd Day-7/`  
  → Moves into Day-7 where pod YAML files are stored.
- `ls`  
  → Lists files (`pod.yaml`, `pod-new.yaml`, `pod-test.yaml`).

---

## Creating Pods Declaratively (YAML)
- `kubectl apply -f pod.yaml`  
  → Applies pod configuration from YAML file.

### Common YAML Errors Encountered
- `unknown field "spec.conatiners"`  
  → Typo in `containers` field.
- `unknown field "spec.conatiner"`  
  → Incorrect key name.
- `json: cannot unmarshal object into Go struct field PodSpec.spec.containers of type []v1.Container`  
  → `containers` must be an array, not an object.
- `unknown field "spec.container"`  
  → Wrong field name; must be `containers`.

---

## Successful Pod Creation
- `kubectl apply -f pod.yaml`  
  → Successfully creates pod `nginx-pod-dec`.
- `kubectl get pods`  
  → Shows both imperative and declarative pods.

---

## Editing Pod Configuration
- `vi pod.yaml`  
  → Edits pod definition file.
- `kubectl apply -f pod.yaml`  
  → Updates existing pod configuration.

---

## Image Pull Errors
- Pod status: `ErrImagePull` / `ImagePullBackOff`  
  → Image name is incorrect or does not exist.

---

## Debugging Pods
- `kubectl describe pod nginx-pod-dec`  
  → Displays detailed pod information including:
  - Image name
  - Container state
  - Events (pull failures, restarts)
- Error shown:  
  → `failed to pull image "nginx123"` (invalid image name).

---

## Fixing Pod Issues
- `kubectl edit pod nginx-pod-dec`  
  → Opens live pod spec for editing.
- Correcting image name fixes `ImagePullBackOff`.
- `kubectl get pods`  
  → Confirms pod is now `Running`.

---

## Exporting Pod YAML
- `kubectl get pod nginx-pod-im -o yaml > pod-imp.yaml`  
  → Exports running pod definition to a YAML file.

---

## Dry Run Pod Creation (YAML)
- `kubectl run nginx-dry-run --image nginx --dry-run=client -o yaml > pod-dry-run.yaml`  
  → Generates pod YAML without creating it.
- `kubectl run nginx-test --image nginx --dry-run=server -o yaml > pod-test-dry.yaml`  
  → Validates YAML against API server without creating pod.

---

## Creating Pod Output in JSON
- `kubectl run nginx-json --image nginx -o json > pod-json.json`  
  → Creates a pod and outputs definition in JSON format.
- `kubectl run nginx-json --image nginx --dry-run=client -o json > pod-json.json`  
  → Generates JSON without creating pod.

---

## Deleting Pods
- `kubectl delete pod nginx-json`  
  → Deletes the specified pod.

---

## Viewing Pods with Labels
- `kubectl get pods --show-labels`  
  → Displays pods along with their labels.

---

## Viewing Pod Details
- `kubectl get pods -o wide`  
  → Shows extended pod information:
  - Pod IP
  - Node name
  - Readiness gates

---

## Key CKA Takeaways
- Imperative commands are faster but less reusable.
- Declarative YAML is preferred for production and exams.
- Most pod failures are due to:
  - YAML syntax errors
  - Wrong image names
- `kubectl describe pod` is the **first command** for debugging.
- `--dry-run=client` is extremely useful in the CKA exam.

---