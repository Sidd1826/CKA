# Kind & Kubectl One-Liner Notes (CKA Prep)

## Shell & Aliases
- `source ~/.bashrc` → Reloads shell configuration (loads aliases like `k`, `kgn`, `kgp`).
- `kgn` → Alias for `kubectl get nodes`.
- `kgp` → Alias for `kubectl get pods`.

---

## Kind Cluster Creation
- `kind create cluster --image kindest/node:v1.35.0@sha256:452d707d4862f52530247495d180205e029056831160e22870e37e3f6c1ac31f --name cka-cluster1` → Creates a single-node kind cluster.
- `kind create cluster --image kindest/node:v1.35.0@sha256:452d707d4862f52530247495d180205e029056831160e22870e37e3f6c1ac31f --name cka-cluster1 --config config.yaml` → Creates a multi-node kind cluster using a config file.
- `ERROR: failed to create cluster: node(s) already exist for a cluster with the name "cka-cluster1"` → Cluster with the same name already exists.
- `kind create cluster --image kindest/node:v1.35.0@sha256:452d707d4862f52530247495d180205e029056831160e22870e37e3f6c1ac31f --name cka-cluster2` → Creates a second kind cluster.

---

## Cluster Info & Verification
- `kubectl cluster-info` → Displays Kubernetes API server and CoreDNS endpoints for the active context.
- `kubectl cluster-info --context kind-cka-cluster1` → Displays cluster info for `cka-cluster1`.
- `kubectl cluster-info --context kind-cka-cluster2` → Displays cluster info for `cka-cluster2`.
- `kubectl version --client` → Displays kubectl client version.
- `kgn` → Verifies control-plane and worker node readiness.

---

## kubeconfig & Context Management
- `kubectl config get-contexts` → Lists all contexts in kubeconfig.
- `kubectl config use-context kind-cka-cluster1` → Switches kubectl to `cka-cluster1`.
- `kubectl config use-context kind-cka-cluster2` → Switches kubectl to `cka-cluster2`.
- `kubectl config delete-context kind-cka-cluster2` → Deletes the context from kubeconfig.
- `kubectl config delete-cluster kind-cka-cluster1` → Deletes the cluster entry from kubeconfig (does NOT delete the kind cluster).

---

## Common Errors & Fixes
- `The connection to the server localhost:8080 was refused - did you specify the right host or port?`  
  → No valid kubeconfig context is set.
- `memcache.go: couldn't get current server API group list`  
  → kubectl is pointing to an invalid or deleted context.
- Fix command:
  - `kubectl config use-context kind-cka-cluster1`

---

## Multiple Clusters Handling
- Each kind cluster runs on a **different localhost port** (example: `https://127.0.0.1:52620`).
- Switching contexts changes which cluster kubectl communicates with.
- Deleting a context does **not** delete the actual kind cluster.

---

## Namespace & Resource Checks
- `kubectl get pods` or `kgp` → Lists pods in the default namespace.
- `kubectl get nodes` or `kgn` → Lists nodes for the active context.

---

## Best Practices (CKA-Oriented)
- Check active context:
  - `kubectl config current-context`
- Properly delete a kind cluster:
  - `kind delete cluster --name cka-cluster1`
  - `kind delete cluster --name cka-cluster2`
- Avoid manually deleting kubeconfig entries unless necessary.
- Use a config file for repeatable multi-node cluster creation.

---
