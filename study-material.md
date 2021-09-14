## Awesome DevOps Resources :computer:


### Kubernetes 

Container Storage Interface (CSI):
- Velotio Blog: https://www.velotio.com/engineering-blog/kubernetes-csi-in-action-explained-with-features-and-use-cases
- CSI offical site:  https://kubernetes-csi.github.io/docs/
- CSI GA release notes: https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/

Custom Resources
- CR: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/
- CRD: https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/ 

Admission Webhooks
- Webhooks: https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/
- Admission Controllers: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
- Good Blog: https://banzaicloud.com/blog/k8s-admission-webhooks/

Helm 
- Chart templates tips: https://helm.sh/docs/howto/charts_tips_and_tricks/
- Helm chart patterns: https://dzone.com/articles/the-art-of-the-helm-chart-patterns-from-the-offici

Security 
- Apparmor: https://kubernetes.io/docs/tutorials/clusters/apparmor/
- Seccomp: https://kubernetes.io/docs/tutorials/clusters/seccomp/

### Prow (Optional)
- Prow Basics: https://github.com/kubernetes/test-infra/tree/master/prow
- Velotio Blog: https://www.velotio.com/engineering-blog/prow-for-native-kubernetes-ci-cd

### Hands-On
Kubernetes Lab:
- K8s playground: https://labs.play-with-k8s.com
- katacoda: https://www.katacoda.com/courses/kubernetes/playground

Excercises: 
- Hostpath CSI Snapshot & Restore: https://github.com/kubernetes-csi/csi-driver-host-path/tree/master/examples
- Setup Cassandra on minikube with hostpath: https://kubernetes.io/docs/tutorials/stateful-application/cassandra/
- Create a helm chart for custom app and host on local helm registry: https://docs.bitnami.com/tutorials/create-your-first-helm-chart/

Optional Excercises:
- Create a Pod with a seccomp profile for syscall auditing: https://kubernetes.io/docs/tutorials/clusters/seccomp/#create-pod-with-seccomp-profile-that-causes-violation
- Setup Prow CI/CD on local minikube/k3s and configure github repo to run pre-submit & periodic jobs: https://github.com/kubernetes/test-infra/blob/master/prow/getting_started_deploy.md
