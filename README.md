# Secure-Kubernetes-Persistent-Storage-with-RBAC-and-Pod-Security-Standards-PSA-

# **Secure Kubernetes Persistent Storage with RBAC and Pod Security Standards (PSA)**

This project demonstrates how to deploy a secure stateful application (e.g., MySQL) in Kubernetes using Persistent Volumes (PVs) and Persistent Volume Claims (PVCs), while adhering to Kubernetes security best practices. The focus is on securing access to persistent storage and enforcing Pod security with Role-Based Access Control (RBAC) and **Pod Security Admission (PSA)**.

### **Project Overview**
- **Objective:** Deploy a stateful application using Persistent Volumes in Kubernetes with security practices like RBAC, PSA, and Secret encryption.
- **Tools Used:** 
  - Minikube (with Docker driver)
  - kubectl
  - Kubernetes RBAC
  - Pod Security Admission (PSA)
  - Kubernetes Secrets
- **Outcome:** A Kubernetes setup that securely handles persistent storage for sensitive workloads while enforcing security policies on Pods.

---

## **Project Structure**


---

## **Steps to Deploy**

Follow the steps below to deploy the application and enforce security standards in your Kubernetes cluster.

### **Step 1: Start Minikube**
Ensure Minikube is running with the Docker driver:

```bash
minikube start --driver=docker

```
Check if minikube is active
