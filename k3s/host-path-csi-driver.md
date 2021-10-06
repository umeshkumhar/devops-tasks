### Installation
k3s site: https://k3s.io/
```bash
curl -sfL https://get.k3s.io | sh -
```
Note: to avoid sudo while using config file pass `--write-kubeconfig-mode 644`[instruction][1] or `chmod 644 /etc/rancher/k3s/k3s.yaml`.

### Start k3s
```bash
sudo k3s server &
```

### Set KUBECONFIG env:
Note: Make sure you have moved all you production config to backup file to avaid accidents.
```bash
mv ~/.kube/config ~.kube/config.prod
```
Use k3s config (By default only privileged user is allowed to acces)
```bash
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```

### Set alias to access k3s cluster
```bash
alias kubectl='sudo k3s kubectl'
```

## Installing hostpath CSI driver
these steps are also documented [here][3]
Note: comand outputs in the above docs might not match your version of k8s.

### install snapshotter:
Clone [external-snapshotter][2] repo
```bash
git clone git@github.com:kubernetes-csi/external-snapshotter.git --depth=1
cd external-snapshotter
```
Install CRDs
```bash
kubectl create -f client/config/crd
```
```text
❯ kg crds | grep snapshot
[sudo] password for wilson: 
volumesnapshotclasses.snapshot.storage.k8s.io    2021-10-06T08:27:30Z
volumesnapshotcontents.snapshot.storage.k8s.io   2021-10-06T08:27:30Z
volumesnapshots.snapshot.storage.k8s.io          2021-10-06T08:27:30Z
```
Install controller
```bash
kubectl create -f deploy/kubernetes/snapshot-controller
```
```text
❯ kgpo -n kube-system -l app=snapshot-controller
NAME                                   READY   STATUS    RESTARTS   AGE
snapshot-controller-6984fdc566-wlqc8   1/1     Running   7          144m
snapshot-controller-6984fdc566-mlsz7   1/1     Running   1          144m
```

### Install hostpath CSI driver:
```bash
git clone git@github.com:kubernetes-csi/csi-driver-host-path.git
cd csi-driver-host-path
./deploy/kubernetes-latest/deploy.sh
```
```text
 ❯ kubectl get pods -o=jsonpath='{range .items[*]}{.metadata.name}{"\n"}{range .spec.containers[*]}{"\t"}{.name}{"\n"}{end}{end}'
snapshot-controller-0
	snapshot-controller
csi-hostpathplugin-0
	hostpath
	csi-external-health-monitor-controller
	node-driver-registrar
	liveness-probe
	csi-attacher
	csi-provisioner
	csi-resizer
	csi-snapshotter
 ```
 ```text
 ❯ kg CSIDriver
NAME                  ATTACHREQUIRED   PODINFOONMOUNT   STORAGECAPACITY   TOKENREQUESTS   REQUIRESREPUBLISH   MODES                  AGE
hostpath.csi.k8s.io   true             true             false             <unset>         false               Persistent,Ephemeral   92m

 ```
[1]: https://github.com/k3s-io/k3s/issues/389#issuecomment-503616742
[2]: https://github.com/kubernetes-csi/external-snapshotter
[3]: https://github.com/kubernetes-csi/csi-driver-host-path/blob/master/docs/deploy-1.17-and-later.md