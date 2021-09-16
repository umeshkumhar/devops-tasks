# Hostpath Backup & Restore

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

## Backup & Restore Steps </br></br>

#### Step 1: Create HostPath StorageClass

    kubectl create -f sc.yaml

#### Step 2: Create PersistentVolumeClaim

    kubectl create -f pvc.yaml

#### Step 3: Create Pod

    kubectl create -f pod.yaml

#### Step 4: Create Files inside the Container

    kubectl exec -it pod-fs /bin/sh
    / # touch /data/my-test
    / # exit

#### Step 5: Create Snapshot

    kubectl create -f snapshot.yaml

#### Step 6: Create Restore PVC

    kubectl create -f restore-pvc.yaml

#### Step 7: Create Restore Pod

    kubectl create -f restore-pod.yaml

#### Step 8: Check if Files exist the Container

    kubectl exec -it restore-pod-fs /bin/sh
    / # ls /data/my-test
    / # exit

</br>

#### Pods

    k get pods
    NAME                   READY   STATUS    RESTARTS   AGE
    pod-fs                 1/1     Running   0          4h34m
    csi-snapshotter-0      3/3     Running   0          3h8m
    csi-hostpath-socat-0   1/1     Running   0          81m
    csi-hostpathplugin-0   8/8     Running   0          81m
    restore-pod-fs         1/1     Running   0          62m

</br>

#### PVs

    k get pv
    NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY     STATUS   CLAIM                    STORAGECLASS      REASON   AGE
    pvc-ee6f5fb9-498d-4765-bb83-66dced7ed0a9   1Gi        RWO            Delete             Bound    default/pvc-fs           csi-hostpath-sc            7h22m
    pvc-abb7b2f1-8eba-4701-ac85-5c6175c4275c   1Gi        RWO            Delete             Bound    default/fs-pvc-restore   csi-hostpath-sc            63m

</br>

#### PVCs

    k get pvc
    NAME             STATUS   VOLUME                                     CAPACITY   ACCESS  MODES   STORAGECLASS      AGE
    pvc-fs           Bound    pvc-ee6f5fb9-498d-4765-bb83-66dced7ed0a9   1Gi            RWO            csi-hostpath-sc   7h23m
    fs-pvc-restore   Bound    pvc-abb7b2f1-8eba-4701-ac85-5c6175c4275c   1Gi            RWO            csi-hostpath-sc   63m

</br>

#### Snapshot

    k get volumesnapshot
    NAME             READYTOUSE   SOURCEPVC   SOURCESNAPSHOTCONTENT   RESTORESIZE       SNAPSHOTCLASS            SNAPSHOTCONTENT                                    CREATIONTIME   AGE
    fs-pv-snapshot   true         pvc-fs                              1Gi               csi-hostpath-snapclass   snapcontent-876e53ca-61b8-4ba1-9cb1-933c71fc159b   66m            66m

</br>

#### Snapshot Contents

    k get volumesnapshotcontents.snapshot.storage.k8s.io
    NAME                                               READYTOUSE   RESTORESIZE     DELETIONPOLICY   DRIVER                VOLUMESNAPSHOTCLASS      VOLUMESNAPSHOT    VOLUMESNAPSHOTNAMESPACE   AGE
    snapcontent-876e53ca-61b8-4ba1-9cb1-933c71fc159b   true         1073741824      Delete           hostpath.csi.k8s.io   csi-hostpath-snapclass   fs-pv-snapshot    default                   67m

</br>

#### Snapshot Classes

    k get volumesnapshotclasses.snapshot.storage.k8s.io
    NAME                     DRIVER                DELETIONPOLICY   AGE
    csi-hostpath-snapclass   hostpath.csi.k8s.io   Delete           7h46m

</br>

#### CRDs

    k get crds
    NAME                                             CREATED AT
    helmchartconfigs.helm.cattle.io                  2021-09-16T07:04:20Z
    addons.k3s.cattle.io                             2021-09-16T07:04:20Z
    helmcharts.helm.cattle.io                        2021-09-16T07:04:20Z
    ingressrouteudps.traefik.containo.us             2021-09-16T07:05:31Z
    tlsoptions.traefik.containo.us                   2021-09-16T07:05:31Z
    serverstransports.traefik.containo.us            2021-09-16T07:05:31Z
    tlsstores.traefik.containo.us                    2021-09-16T07:05:31Z
    traefikservices.traefik.containo.us              2021-09-16T07:05:31Z
    ingressroutes.traefik.containo.us                2021-09-16T07:05:31Z
    middlewares.traefik.containo.us                  2021-09-16T07:05:31Z
    ingressroutetcps.traefik.containo.us             2021-09-16T07:05:31Z
    volumesnapshotclasses.snapshot.storage.k8s.io    2021-09-16T07:24:49Z
    volumesnapshotcontents.snapshot.storage.k8s.io   2021-09-16T07:24:49Z
    volumesnapshots.snapshot.storage.k8s.io          2021-09-16T07:24:49Z

