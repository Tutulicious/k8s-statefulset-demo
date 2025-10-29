🌐 Kubernetes StatefulSet Demo: Unique Identity & Persistent Storage

A hands-on project demonstrating the deployment and management of a fault-tolerant, stateful web application using core Kubernetes primitives. This project showcases the fundamental principles required to manage complex distributed data platforms like Kafka, OpenSearch, or databases on Kubernetes.

🚀 Overview

This repository contains the necessary YAML manifests to deploy a 3-replica Nginx cluster managed by a StatefulSet.

The core objective is to ensure each application instance (Pod) has a stable, unique identity and dedicated persistent storage that survives pod deletion and rescheduling. This is achieved by:

Using a Headless Service for stable network identity.

Employing volumeClaimTemplates for dedicated Persistent Volume Claims (PVCs).

Utilizing an initContainer to inject a unique state (the Pod's hostname) onto its volume upon startup, proving persistence.

💡 The Learning Takeaway: Code is Key

This project strongly reinforces the principle that fundamental understanding comes from writing and debugging the code itself. The initial process involved crucial troubleshooting of indentation errors (volumeClaimTemplates placement) and typos, which provided a deeper comprehension of the Kubernetes API schema than simple copy-pasting ever could. This hands-on, troubleshooting approach is vital for real-world DevOps maturity.

🛠️ Prerequisites

A local Kubernetes cluster (Docker Desktop Kubernetes, Minikube, or K3s).

kubectl configured to connect to your cluster.

The Git CLI installed and configured (especially with the Git Credential Manager for token caching on WSL).

(Recommended) WSL 2 (Ubuntu/Debian) environment for seamless testing and troubleshooting.

⚙️ Deployment Guide (GitOps Workflow)

Follow these steps to deploy and test the application on your cluster.

1. Clone the Repository

Clone this repository to your local machine:

git clone [https://github.com/Tutulicious/k8s-statefulset-demo.git](https://github.com/Tutulicious/k8s-statefulset-demo.git)
cd k8s-statefulset-demo


2. Apply the Manifests

The kubectl apply command will create both the Headless Service (nginx) and the StatefulSet (web).

kubectl apply -f .


3. Verification & Status Check

The StatefulSet will create three resources sequentially (web-0, web-1, web-2), binding a unique PVC to each one. Watch the status of the deployment, waiting for all resources to become ready:

# Watch the status of the deployment (StatefulSet, Pods, and PVCs)
kubectl get statefulset,pod,pvc -w


Wait until the web StatefulSet is 3/3 ready, and all PVCs are Bound.

Resource Type

Expected Count

Key Status

Pods

3

Running

StatefulSet

1

3/3 Ready

PVCs

3

Bound

4. Test Persistence and Identity

Use kubectl port-forward to establish a direct connection from your host machine (Windows/WSL) to a specific Pod. This proves that the initContainer successfully wrote a unique, persistent file to that Pod's dedicated volume.

# Forward local port 8080 to Pod 'web-1' port 80
kubectl port-forward web-1 8080:80


Open your web browser and navigate to http://localhost:8080.

Expected output:

<h1>My hostname is web-1</h1>


🗑️ Cleanup

When you are done, clean up all resources from your cluster.

Stop the port-forward command (Ctrl+C).

Delete the Service and StatefulSet:

kubectl delete -f .


Manually delete the PVCs (required because StatefulSets do not delete PVCs by default, protecting your data):

kubectl delete pvc www-web-0 www-web-1 www-web-2


📄 Manifests

File

Resource Kind

Purpose

headless-service.yaml

Service

Creates a stable DNS entry for each Pod, essential for StatefulSet communication.

statefulset.yaml

StatefulSet

Manages the 3 replicas, defines the Pod's lifecycle, and includes the volumeClaimTemplates to define persistence.
