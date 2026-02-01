**Kubernetes Commands Walkthrough (Day 8)**
===========================================

This document gives **one-line explanations** for the Kubernetes commands you ran, in the **same flow/order**, so you can revise quickly for CKA or interviews.

**Shell & Environment**
-----------------------

*   Prints the current working directory.
    
*   Reloads shell configuration so kubectl aliases like k are available.
    
*   Lists files in the directory sorted by time (oldest first) with details.
    

**Applying Manifests & Errors**
-------------------------------

*   Applies the Kubernetes manifest defined in task.yaml.
    
*   Happens because matchLabels must be a key-value map, not a string.
    
*   Selector labels must exactly match pod template labels.
    
*   spec.selector of a ReplicaSet cannot be changed once created.
    

**Inspecting Pods**
-------------------

*   Lists all pods in the current namespace.
    

**ReplicaSet Operations**
-------------------------

*   ❌ Invalid because kubectl edit does not support --image flag.
    
*   Opens the manifest file to manually edit image or labels.
    
*   Deletes the ReplicaSet and all pods managed by it.
    

**Deployment Creation & Inspection**
------------------------------------

*   Creates or updates a Deployment based on the manifest.
    
*   Shows detailed information about the Deployment, pods, and rollout status.
    
*   Lists all Kubernetes resources in the namespace.
    

**Rollouts & Versioning**
-------------------------

*   Displays revision history of the Deployment.
    
*   Rolls back the Deployment to a previous revision.
    

**Final Deployment Example (task\_2.yaml)**
-------------------------------------------

*   Creates a Deployment using correct selector–label matching.
    
*   Confirms that pods are created and transitioning to running state.
    

**Key Learnings (Important for CKA)**
-------------------------------------

*   selector.matchLabels **must match** template.metadata.labels.
    
*   ReplicaSet selectors are **immutable**.
    
*   Image updates should be done via **Deployment**, not ReplicaSet.
    
*   Deployments manage ReplicaSets automatically.
    
*   Rollout history + undo are **Deployment-only features**.
    

✅ This workflow perfectly demonstrates **ReplicaSet vs Deployment**, **immutability**, and **rolling updates** — exactly what CKA tests.