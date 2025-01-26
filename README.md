# Secure Kubernetes Persistent Storage with RBAC and Pod Security Standards PSA

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

**Step 2: Create a Namespace**

Create a dedicated namespace for the project:

```bash
kubectl create namespace secure-storage

```
Step 3: Implement RBAC for Access Control
Create a Role that grants permissions to manage Persistent Volume Claims (PVCs) and Pods:

```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: secure-storage
  name: storage-manager-role
rules:
- apiGroups: [""]
  resources: ["pods", "persistentvolumeclaims"]
  verbs: ["get", "list", "create", "delete"]

```
Create a RoleBinding to bind the Role to the ServiceAccount:

```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: storage-manager-binding
  namespace: secure-storage
subjects:
- kind: ServiceAccount
  name: default
  namespace: secure-storage
roleRef:
  kind: Role
  name: storage-manager-role
  apiGroup: rbac.authorization.k8s.io

```
Step 4: Set Up Persistent Storage
Create a Persistent Volume (PV) using local storage:

```bash
apiVersion: v1
kind: PersistentVolume
metadata:
  name: secure-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"

```
Create a Persistent Volume Claim (PVC) to request storage from the PV:

```bash

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: secure-pvc
  namespace: secure-storage
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi

```
Step 5: Deploy a Secure Stateful Application (MySQL)
Create a Secret to store MySQL credentials:

```bash

kubectl create secret generic mysql-secret \
  --from-literal=mysql-root-password=root_password \
  --from-literal=mysql-user=user \
  --from-literal=mysql-password=user_password \
  --namespace secure-storage

```
Deploy MySQL using a StatefulSet with PVC and Secret:

```bash

apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: secure-mysql
  namespace: secure-storage
spec:
  selector:
    matchLabels:
      app: secure-mysql
  serviceName: "mysql-service"
  replicas: 1
  template:
    metadata:
      labels:
        app: secure-mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        ports:
        - containerPort: 3306
          name: mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: mysql-root-password
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: mysql-user
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: mysql-password
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
  volumeClaimTemplates:
  - metadata:
      name: mysql-storage
    spec:
      accessModes: ["ReadWriteOnce"]
      resources:
        requests:
          storage: 500Mi

```
Step 6: Enforce Pod Security Standards (PSA)
Apply the restricted policy for the namespace:

```
kubectl label namespace secure-storage pod-security.kubernetes.io/enforce=restricted --overwrite

```
Step 7: Test Security Measures
Verify RBAC Permissions: Test restricted actions outside the defined role scope.

```bash

kubectl delete pod --namespace kube-system

```
Inspect Pod Logs: Check the logs for any security-related events:

```bash 

kubectl logs -n secure-storage -l app=secure-mysql

```
Simulate Unauthorized Access: Attempt to deploy a non-compliant Pod to test enforcement:

```bah

apiVersion: v1
kind: Pod
metadata:
  name: test-pod
  namespace: secure-storage
spec:
  containers:
  - name: nginx
    image: nginx


```
Outcomes

Secure Storage: Persistent Volumes and Claims are securely managed using RBAC and Secrets.
Enforced Policies: Pod Security Admission (PSA) ensures Pods comply with security standards.
Hands-On Learning: This project strengthens your understanding of combining Kubernetes security and storage management.

Contributions

If you want to contribute to this project, feel free to fork it and submit a pull request. If you encounter any issues or have suggestions, please open an issue on GitHub.










