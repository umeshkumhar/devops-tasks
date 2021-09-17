# Setup Cassandra with Hostpath on K3s

## Prerequisite Steps </br></br>

#### Step 1: Created k3s Cluster

    curl -sfL https://get.k3s.io | sh -

#### Step 2: Install CSI Driver for Hostpath

- Clone the Gitrepo **https://github.com/kubernetes-csi/csi-driver-host-path**
- Navigate to the Gitrepo and **csi-driver-host-path**
- Run the below command [**Note**: use appropriate k8s version]

        deploy/kubernetes-1.21/deploy.sh

#### Step 3: Clone the Gitrepo **https://github.com/kubernetes-csi/external-snapshotter**

#### Step 4: Install Snapshot CRDs
- Navigate to the Gitrepo **external-snapshotter**
- Execute the below command

        kubectl create -f client/config/crd

#### Step 5: Install Common Snapshot Controller
- Navigate to the Gitrepo **external-snapshotter**
- Execute the below command

        kubectl create -f deploy/kubernetes/snapshot-controller

#### Step 6: Install CSI Driver
- Navigate to the Gitrepo **external-snapshotter**
- Execute the below command

        kubectl create -f deploy/kubernetes/csi-snapshotter

## Cassandra Steps </br></br>

#### Step 1: Create Cassandra Service

    kubectl apply -f svc.yaml 

#### Step 2: Create Cassandra StorageClass

    kubectl apply -f sc.yaml 

#### Step 3: Create Cassandra Statefulset

    kubectl apply -f statefulset.yaml 

#### Step 4: Check Cassandra resources

    k get pods,svc,sc
    NAME                       READY   STATUS    RESTARTS   AGE
    pod/csi-snapshotter-0      3/3     Running   0          9m6s
    pod/csi-hostpath-socat-0   1/1     Running   0          8m10s
    pod/csi-hostpathplugin-0   8/8     Running   0          8m12s
    pod/cassandra-0            0/1     Running   0          5m3s
    
    NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)           AGE
    service/kubernetes         ClusterIP   10.43.0.1       <none>        443/TCP           12m
    service/csi-snapshotter    ClusterIP   10.43.142.248   <none>        12345/TCP         9m6s
    service/hostpath-service   NodePort    10.43.105.175   <none>        10000:32721/TCP   8m10s
    service/cassandra          ClusterIP   None            <none>        9042/TCP          5m16s
    
    NAME                                               PROVISIONER             RECLAIMPOLICY    VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
    storageclass.storage.k8s.io/local-path (default)   rancher.io/local-path   Delete           WaitForFirstConsumer   false                  12m
    storageclass.storage.k8s.io/fast                   hostpath.csi.k8s.io     Delete           WaitForFirstConsumer   false                  5m11s
    


#### Step 5: Get Cassandra Status

    kubectl exec -it cassandra-0 -- nodetool status
    Datacenter: DC1-K8Demo
    ======================
    Status=Up/Down
    |/ State=Normal/Leaving/Joining/Moving
    --  Address     Load       Tokens       Owns (effective)  Host  ID                               Rack
    UN  10.42.0.14  99.59 KiB  32           100.0%              37fa9547-8cf0-438c-91b5-78575044326e  Rack1-K8Demo