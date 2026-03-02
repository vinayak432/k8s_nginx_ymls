# kube_nginx_ymls

# 🚀 Kubernetes Nginx Scheduling Lab

This repository contains Kubernetes YAML manifests used to understand and test:

- Pods
- ReplicaSets
- Deployments
- DaemonSets
- Services
- Taints & Tolerations
- NodeSelector
- NodeAffinity
- PodAffinity
- PodAntiAffinity

All examples are tested on a **3-node cluster**:

- 1 Control Plane (tainted)
- 2 Worker Nodes

---

## 🖥️ Cluster Setup

| Node | Role | Notes |
|------|------|------|
| Control Plane | Master | Tainted (no scheduling) |
| Worker 1 | Compute | size=big |
| Worker 2 | Compute | size=big |

Available node labels:


kubernetes.io/hostname
size=big


(No zone labels available)

---

## 📦 Workloads Used

| App | Label |
|-----|------|
| Backend | `app=backend` |
| Nginx | `app=nginx` |

---

## 📂 Repository Contents

| File | Purpose |
|------|--------|
| nginx-pod.yaml | Basic Pod |
| nginx-rs.yaml | ReplicaSet |
| nginx-rs-toleration.yaml | RS with tolerations |
| nginx-daemonset.yaml | DaemonSet |
| nginx_deployment.yaml | Rolling update deployment |
| nginx_service_lb.yaml | LoadBalancer service |
| nginx_deploy_nodeselector.yaml | NodeSelector scheduling |
| nginx_deploy_nodeaff_hard_rule.yaml | Hard NodeAffinity |
| nginx_deploy_nodeaff_soft_rule.yaml | Soft NodeAffinity |
| nginx_backend.yaml | Backend deployment |
| nginx_deploy_podaff.yaml | PodAffinity |
| nginx_deploy_podantiaff.yaml | PodAntiAffinity |

---

## 🧠 Scheduling Concepts Covered

### 1️⃣ NodeSelector
Schedule pods on nodes with matching labels.

Example:

size=big


---

### 2️⃣ NodeAffinity

#### Hard Rule

requiredDuringSchedulingIgnoredDuringExecution

Pod MUST match node label.

#### Soft Rule

preferredDuringSchedulingIgnoredDuringExecution

Scheduler tries but not mandatory.

---

### 3️⃣ PodAffinity

Used to **co-locate pods**.

Example:
- Run nginx near backend pods
- Uses pod labels (not node labels)

Topology used:

kubernetes.io/hostname


---

### 4️⃣ PodAntiAffinity

Used to **spread pods apart**.

Example:
- Prevent nginx replicas from running on same node
- Improves High Availability (HA)

---

## ⚠️ Important Learnings

| Scenario | Result |
|----------|--------|
| Hard PodAffinity without matching pods | Pods Pending |
| Hard PodAntiAffinity with 2 nodes & 3 replicas | 1 Pending |
| replicas ≤ nodes | All Running |
| replicas > nodes (hard anti-affinity) | Extra pods Pending |
| AntiAffinity label absent in cluster | Rule ignored |

---

## 📊 Behaviour Observed

### PodAntiAffinity

| Replicas | Nodes | Result |
|----------|-------|--------|
| 2 | 2 | Spread perfectly |
| 3 | 2 | 1 Pending |
| 4 | 2 | 2 Pending |

---

### AntiAffinity Selector Impact

| Selector | Meaning |
|----------|--------|
| app=nginx | Spread nginx |
| app=backend | Avoid backend nodes |
| No matching pods | Rule ignored |

---

## 🛠️ Apply Examples

Deploy backend:


kubectl apply -f nginx_backend.yaml


Deploy nginx with PodAffinity:


kubectl apply -f nginx_deploy_podaff.yaml


Deploy nginx with PodAntiAffinity:


kubectl apply -f nginx_deploy_podantiaff.yaml


---

## 🧹 Cleanup


kubectl delete deployment --all


---

## 🎯 Goal

This repo is built for **hands-on understanding of Kubernetes scheduling behaviour**, especially:

- Node-based placement
- Pod co-location
- Pod spreading for HA

---

## 📘 Key Takeaway

PodAffinity = Bring pods together  
PodAntiAffinity = Spread pods apart  
NodeAffinity = Choose where to run

---

Happy Learning 🚀
