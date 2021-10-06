### Backup and restore using hostpath-csi
create storage class for csi-hostpath
```bash
kubectl create -f storageclass.yaml
```
```text
❯ kg sc
NAME                   PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
local-path (default)   rancher.io/local-path   Delete          WaitForFirstConsumer   false                  26h
csi-hostpath-sc        hostpath.csi.k8s.io     Delete          WaitForFirstConsumer   true                   4h58m
```
Create new pvc using above storage class
```bash
kubectl create -f pvc.yaml
```
```text
❯ kg pvc
NAME              STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
csi-pvc           Bound    pvc-5586c75a-efd6-4976-a330-382a0acc26e5   1Gi        RWO            csi-hostpath-sc   4h13m

```

Create simple deployment to use above pvc
```bash
kubectl create -f app.yaml
```
```text
❯ kgpo
NAME                               READY   STATUS    RESTARTS   AGE
sample-nginx-6dfd989449-rrwj6      1/1     Running   1          109m
csi-hostpathplugin-0               8/8     Running   8          68m
csi-hostpath-socat-0               1/1     Running   1          68m
```

Create sample data:
```bash
kubectl exec -it POD_NAME -- bash -c "echo $HOSTNAME > /data/sample.txt"
```
```text
❯ kex sample-nginx-6dfd989449-rrwj6 -- cat /data/sample.txt
sample-nginx-6dfd989449-rrwj6
```

Create Snapshot:
```bash
kubectl create -f snapshot.yaml
```
```text
❯ kg volumesnapshot
NAME            READYTOUSE   SOURCEPVC   SOURCESNAPSHOTCONTENT   RESTORESIZE   SNAPSHOTCLASS            SNAPSHOTCONTENT                                    CREATIONTIME   AGE
snapshot-demo   true         csi-pvc                             1Gi           csi-hostpath-snapclass   snapcontent-f3acfe07-7faf-4e98-8590-d599d7315019   43m            107m
```

Create new pvc from nsapshot:
```bash
kubectl create -f preloaded-pvc.yaml
```
```text
❯ kgpvc
NAME              STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
csi-pvc           Bound    pvc-5586c75a-efd6-4976-a330-382a0acc26e5   1Gi        RWO            csi-hostpath-sc   4h18m
csi-pvc-restore   Bound    pvc-22340577-5dbb-40a6-9a92-8e9619be5255   1Gi        RWO            csi-hostpath-sc   98m
```

Create new app instance using preloaded pvc
```bash
kubectl create -f preloaded-app.yaml
```
```text
❯ kgpo
NAME                               READY   STATUS    RESTARTS   AGE
sample-nginx-6dfd989449-rrwj6      1/1     Running   1          114m
csi-hostpathplugin-0               8/8     Running   8          73m
csi-hostpath-socat-0               1/1     Running   1          73m
preloaded-nginx-7894c8c54f-758b9   1/1     Running   0          32m
```
Verify restored data:
```bash
kubectl exec -it RESTORED_POD_NAME -- cat  /data/sample.txt
```
Result should match source POD_NAME
```text
❯ kex preloaded-nginx-7894c8c54f-758b9 -- cat /data/sample.txt
sample-nginx-6dfd989449-rrwj6
```
