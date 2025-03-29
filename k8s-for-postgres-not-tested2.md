Here’s a **detailed step-by-step lab guide** for setting up **Kubernetes for PostgreSQL DBA** with **Patroni-based HA, Backup Strategies, and Performance Tuning**.

---

# **Kubernetes for PostgreSQL DBA - Hands-On Lab Guide**

## **1. Prerequisites**
Before we begin, ensure you have:
- A **Kubernetes Cluster** (GKE, AKS, EKS, or Minikube)
- **kubectl** installed and configured
- **Helm** installed (for deploying PostgreSQL Operator)
- Basic knowledge of PostgreSQL

---

## **2. Deploying PostgreSQL in Kubernetes**
### **Step 1: Install PostgreSQL Operator using Helm**
Run the following command to deploy a PostgreSQL operator:

```bash
helm repo add zalando https://opensource.zalando.com/postgres-operator/charts/postgres-operator
helm repo update
helm install pg-operator zalando/postgres-operator
```

Verify the installation:

```bash
kubectl get pods -n default
```

---

### **Step 2: Deploy a PostgreSQL Cluster**
Create a `postgresql-cluster.yaml` file:

```yaml
apiVersion: "acid.zalan.do/v1"
kind: postgresql
metadata:
  name: pg-cluster
spec:
  teamId: "team1"
  volume:
    size: 10Gi
  numberOfInstances: 3
  users:
    dba:  # Database user
      - superuser
      - createdb
  databases:
    appdb: dba  # Create 'appdb' owned by 'dba'
  postgresql:
    version: "16"
```

Apply the configuration:

```bash
kubectl apply -f postgresql-cluster.yaml
```

Verify cluster:

```bash
kubectl get pods
```

---

## **3. High Availability with Patroni**
### **Step 3: Deploy Patroni**
Patroni automatically manages leader election and failover.

Modify `patroni.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: patroni-config
data:
  PATRONI_POSTGRESQL_CONFIG: |
    synchronous_commit = 'on'
    max_connections = 200
  PATRONI_REPLICATION_MODE: 'synchronous'
```

Apply the configuration:

```bash
kubectl apply -f patroni.yaml
```

To check the leader node:

```bash
kubectl exec -it pg-cluster-0 -- patronictl list
```

---

## **4. Backup and Restore Strategy**
### **Step 4: Install pgBackRest for Backup**
Create a backup configuration in `pgbackrest.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: pgbackrest-config
data:
  pgbackrest.conf: |
    [global]
    repo1-path=/var/lib/pgbackrest
    repo1-retention-full=3
    repo1-retention-diff=7
```

Apply:

```bash
kubectl apply -f pgbackrest.yaml
```

Trigger a full backup:

```bash
kubectl exec -it pg-cluster-0 -- pgbackrest --stanza=pg-cluster backup
```

---

## **5. Performance Tuning**
### **Step 5: Optimize PostgreSQL Parameters**
Edit the `patroni.yaml` file:

```yaml
data:
  PATRONI_POSTGRESQL_CONFIG: |
    shared_buffers = '2GB'
    work_mem = '64MB'
    maintenance_work_mem = '512MB'
    effective_cache_size = '6GB'
    autovacuum_max_workers = 3
```

Apply the config:

```bash
kubectl apply -f patroni.yaml
```

Restart pods:

```bash
kubectl rollout restart statefulset pg-cluster
```

---

## **6. Monitoring**
### **Step 6: Deploy PostgreSQL Exporter for Prometheus**
Install Prometheus PostgreSQL Exporter:

```bash
helm install postgres-exporter prometheus-community/prometheus-postgres-exporter
```

Verify metrics:

```bash
kubectl get svc
```

Access via Prometheus:

```bash
http://<prometheus-ip>:9090
```

---

# **Conclusion**
This lab covered:
✅ Deploying PostgreSQL on Kubernetes  
✅ Setting up HA with Patroni  
✅ Configuring automated backups  
✅ Performance tuning best practices  
✅ Setting up monitoring  

