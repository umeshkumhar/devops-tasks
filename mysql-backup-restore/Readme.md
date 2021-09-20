## 1. Installation

### 1) Create the GKE Cluster with CSI drive enabled using below command

```bash
$ gcloud container clusters create CLUSTER-NAME \
        --addons=GcePersistentDiskCsiDriver \
        --cluster-version=VERSION
```

### 2) To enable the Compute Engine persistent disk CSI Driver in existing Standard clusters, use the gcloud command-line tool or the Google Cloud Console.

```bash
$ gcloud container clusters update CLUSTER-NAME \
   --update-addons=GcePersistentDiskCsiDriver=ENABLED
```

### 3) Now install the TVK operator using below commands
```bash
helm repo add triliovault-operator http://charts.k8strilio.net/trilio-stable/k8s-triliovault-operator
helm repo add triliovault http://charts.k8strilio.net/trilio-stable/k8s-triliovault
helm repo update    
helm install triliovault-operator triliovault-operator/k8s-triliovault-operator
```

### 4) Install the TVK licences in default namespace (before applying update the key in below yaml)
```bash
    $ kubectl apply -f licence.yaml -n default
```    

### 4) Now install trillioValut Manager using below manifests
```yaml
apiVersion: triliovault.trilio.io/v1
kind: TrilioVaultManager
metadata:
  labels:
    triliovault: triliovault
  name: triliovault-manager
  namespace: default
spec:
  trilioVaultAppVersion: 2.5.0
  helmVersion:
    version: v3
  applicationScope: Cluster
```

## 2. Create a Target, Policy and Backup Plan.
```bash
kubectl apply -f target.yaml
kubectl apply -f sample_policy.yaml
kubectl apply -f tvl_bkp_plan.yaml
```

> $ kubectl get targets,policies,backupplans
```
NAME                                               TYPE   THRESHOLD CAPACITY   VENDOR   STATUS      BROWSING ENABLED
target.triliovault.trilio.io/mysql-sample-target   NFS    10Gi                 Other    Available   

NAME                                               POLICY      DEFAULT
policy.triliovault.trilio.io/mysql-sample-policy   Retention   false

NAME                                                 TARGET                RETENTION POLICY      INCREMENTAL SCHEDULE   FULL BACKUP SCHEDULE   STATUS
backupplan.triliovault.trilio.io/mysql-backup-plan   mysql-sample-target   mysql-sample-policy                                                 Available

```


## 3. Now create mysql deployment and pvc using below command
```bash
kubectl apply -f pvc.yaml
kubectl apply -f deployment.yaml
```

## 4. Go inside mysql pod and create the table

```sql
create database soccer; 
show databases;
use soccer;

create table leagues
(
league_id int auto_increment primary key,
name varchar(30) unique,
country varchar(40),
    matches int default 0,
    goals int default 0
);

insert into leagues(country,name)
values("English","EPL"),
("France","Ligue 1"),
("Italy","Serie A"),
("Spain","La Liga"),
("Germany","Bundesliga");
    
SELECT * from leagues;

```
## 5. Now take the backup of our application and then remove our app using below command

```bash
kubectl apply -f bkp.yaml
sleep 3m
kubectl delete -f deployment.yaml
kubectl delete -f pvc.yaml
```

## 6. Now restore the backup and we can see our mysql db with all our old data
```bash
kubectl apply -f restore.yaml
kubectl exec -it deploy/k8s-demo-app-mysql -- bash

```
```sql
show databases;
use soccer;
SELECT * from leagues;
```