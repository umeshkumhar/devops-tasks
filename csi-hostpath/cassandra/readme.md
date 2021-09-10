# Errors I faced doing this tasks and references to fix them

- When deploying csi-hostpath in microk8s disable topology feature
    - https://kubernetes-csi.github.io/docs/topology.html#sidecar-deployment
    - Also change kubelet path to `/var/snap/microk8s/common/var/lib/kubelet`
        - https://github.com/kubernetes-csi/csi-driver-host-path/issues/71#issuecomment-511515285
        - https://www.gitmemory.com/issue/kubernetes-csi/csi-driver-host-path/107/546011257
- Topology key not found error
    - https://github.com/hetznercloud/csi-driver/issues/92#issuecomment-739069400
