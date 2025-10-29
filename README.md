# 🌐 Kubernetes StatefulSet Demo: Unique Identity & Persistent Storage

A hands-on project demonstrating the deployment and management of a fault-tolerant, stateful web application using core Kubernetes primitives.  
This project showcases the fundamental principles required to manage complex distributed data platforms like **Kafka**, **OpenSearch**, or **databases** on Kubernetes.

---

## 🚀 Overview

This repository contains the necessary YAML manifests to deploy a **3-replica Nginx cluster** managed by a **StatefulSet**.

The core objective is to ensure each application instance (Pod) has a **stable, unique identity** and **dedicated persistent storage** that survives pod deletion and rescheduling.  
This is achieved by:

1. Using a **Headless Service** for stable network identity.  
2. Employing **`volumeClaimTemplates`** for dedicated Persistent Volume Claims (PVCs).  
3. Utilizing an **`initContainer`** to inject a unique state (the Pod's hostname) onto its volume upon startup, proving persistence.

---

## 💡 The Learning Takeaway: *Code is Key*

**This project strongly reinforces the principle that fundamental understanding comes from writing and debugging the code itself.**  
The initial process involved crucial troubleshooting of indentation errors (`volumeClaimTemplates` placement) and typos, which provided a deeper comprehension of the Kubernetes API schema than simple copy-pasting ever could.  
This hands-on, troubleshooting approach is vital for real-world DevOps maturity.

---

## 🛠️ Prerequisites

Before starting, make sure you have:

- A local Kubernetes cluster (**Docker Desktop**, **Minikube**, or **K3s**)
- `kubectl` configured to connect to your cluster
- **Git CLI** installed and configured (with Git Credential Manager for token caching on WSL)
- *(Recommended)* **WSL 2 (Ubuntu/Debian)** environment for seamless testing and troubleshooting

---

## ⚙️ Deployment Guide (GitOps Workflow)

Follow these steps to deploy and test the application on your cluster.

---

### 1. Clone the Repository

Clone this repository to your local machine and navigate to it:

    git clone https://github.com/Tutulicious/k8s-statefulset-demo.git
    cd k8s-statefulset-demo

---

### 2. Apply the Manifests

Deploy both the Headless Service (`nginx`) and StatefulSet (`web`):

    kubectl apply -f .

---

### 3. Verification & Status Check

The StatefulSet will create three resources sequentially (`web-0`, `web-1`, `web-2`), binding a unique PVC to each one.  
Watch the status of the deployment and wait for all resources to become ready:

    # Watch the status of the deployment (StatefulSet, Pods, and PVCs)
    kubectl get statefulset,pod,pvc -w

Wait until the StatefulSet is **3/3 Ready** and all PVCs are **Bound**.

| Resource Type | Expected Count | Key Status |
|----------------|----------------|-------------|
| Pods | 3 | Running |
| StatefulSet | 1 | 3/3 Ready |
| PVCs | 3 | Bound |

---

### 4. Test Persistence and Identity

Use `kubectl port-forward` to connect from your host machine (Windows/WSL) to a specific Pod.  
This demonstrates that the `initContainer` successfully wrote a unique, persistent file to the Pod’s dedicated volume.

    # Forward local port 8080 to Pod 'web-1' port 80
    kubectl port-forward web-1 8080:80

Now open your browser and visit:

👉 [http://localhost:8080](http://localhost:8080)

Expected output:

```html
<h1>My hostname is web-1</h1>
```

---

### 🗑️ Cleanup

When done, clean up all resources from your cluster.

Stop the port-forward command (`Ctrl + C`), then delete the Service and StatefulSet:

    kubectl delete -f .

Manually delete the PVCs (StatefulSets do **not** delete PVCs by default, to protect your data):

    kubectl delete pvc www-web-0 www-web-1 www-web-2

---

## 📄 Manifests

| File | Resource Kind | Purpose |
|------|----------------|----------|
| `headless-service.yaml` | Service | Creates a stable DNS entry for each Pod — essential for StatefulSet communication. |
| `statefulset.yaml` | StatefulSet | Manages the 3 replicas, defines the Pod’s lifecycle, and includes the `volumeClaimTemplates` for persistence. |

---

## 🧠 Key Concepts Reinforced

- **StatefulSet Identity:** predictable naming and ordered deployment  
- **Headless Services:** enabling per-pod DNS resolution  
- **Persistent Volumes:** ensuring durable, Pod-specific data  
- **Init Containers:** preparing state before main containers start

---

## 🧩 Example Output Snapshot

```
NAME         READY   AGE
pod/web-0    1/1     2m
pod/web-1    1/1     2m
pod/web-2    1/1     2m

NAME                    READY   AGE
statefulset.apps/web    3/3     2m

NAME              STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS
pvc/www-web-0     Bound    pvc-0a7b99b7-6d2f-4eaa-a7b0-1234567890ab   1Gi        RWO            standard
pvc/www-web-1     Bound    pvc-1b8c00c8-7e3c-4b33-b7c1-0987654321cd   1Gi        RWO            standard
pvc/www-web-2     Bound    pvc-2c9d11d9-8f4d-4c44-c8d2-abcdef123456   1Gi        RWO            standard
```

---

## 🧭 Inspiration

This demo serves as a foundational exercise for learning **stateful workload management**, which scales directly into production-grade setups for:

- Apache **Kafka**, **ZooKeeper**  
- **PostgreSQL** / **MongoDB**  
- **OpenSearch**, **Elasticsearch**

Understanding this demo brings you one step closer to mastering **distributed data systems on Kubernetes**.

---

### ✨ Author

**Lazar Tutulić (Tutulicious)**  
💼 DevOps / Cloud SRE  
📍 Belgrade, Serbia  
🌐 [GitHub](https://github.com/Tutulicious)
