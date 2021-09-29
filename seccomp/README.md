# Seccomp on Kind K8s Cluster 

### Create the kind cluster on the localhost:
    
    kind create cluster --config=Kind.yaml

    Creating cluster "kind" ...
     ✓ Ensuring node image (kindest/node:v1.21.1) 🖼
     ✓ Preparing nodes 📦
     ✓ Writing configuration 📜
     ✓ Starting control-plane 🕹️
     ✓ Installing CNI 🔌
     ✓ Installing StorageClass 💾
    Set kubectl context to "kind-kind"
    You can now use your cluster with:

    kubectl cluster-info --context kind-kind

    Have a nice day! 👋