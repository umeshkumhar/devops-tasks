## Steps to configure Seccomp

1. Install the cert manager and seccomp operator using below command

```yaml
$ kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.5.3/cert-manager.yaml
$ kubectl --namespace cert-manager wait --for condition=ready pod -l app.kubernetes.io/instance=cert-manager
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/security-profiles-operator/master/deploy/operator.yaml

```
2. Verify the installation by checking the pods in respective namespace


    $ kubectl get pods -n cert-manager

    ```css
    NAME                                                 READY   STATUS    RESTARTS   AGE
    security-profiles-operator-5b6fc967bb-9gdkj          1/1     Running   0          90m
    security-profiles-operator-5b6fc967bb-f6vc4          1/1     Running   0          90m
    security-profiles-operator-5b6fc967bb-fkqkh          1/1     Running   0          90m
    security-profiles-operator-webhook-cc5db4b89-5sjtv   1/1     Running   0          90m
    security-profiles-operator-webhook-cc5db4b89-724dr   1/1     Running   0          90m
    security-profiles-operator-webhook-cc5db4b89-pzrph   1/1     Running   0          90m
    spod-4l6hn                                           2/2     Running   0          90m
    spod-qwkg6                                           2/2     Running   0          90m
    spod-qwl2p                                           2/2     Running   0          90m
    ```

    $ kubectl get pods -n security-profiles-operator
    
    ```css
    NAME                                      READY   STATUS    RESTARTS   AGE
    cert-manager-848f547974-qvrvs             1/1     Running   0          118m
    cert-manager-cainjector-54f4cc6b5-4k9t5   1/1     Running   0          118m
    cert-manager-webhook-58fb868868-mrw6k     1/1     Running   0          118m

    ```

3. Now create seccomp profiles using below command

    ```bash
    kubectl apply -f seccomp_configmap.yaml
    ```
    ```bash
    kubectl get seccompprofiles.security-profiles-operator.x-k8s.io
    ```
    ```css
    NAME           STATUS      AGE
    default        Installed   72m
    fine-grained   Installed   72m
    violation      Installed   72m
    ```

4. Now we can use above **Seccomp** profiles inside our deployment using below commands.

    ```yaml
    spec:
        securityContext:
            seccompProfile:
            type: Localhost
            localhostProfile: operator/default/fine-grained.json
        containers:
        ....
        ....

    ```
    ```bash
    kubectl apply -f all_allow_pod.yaml
    kubectl apply -f fine_grained_pod.yaml.yaml
    kubectl apply -f violating_pod.yaml
    ``` 

5. Expected output

    ``` kubectl get pod | grep -iE "restart|audit"  ```

    ```css
    NAME                                                             READY   STATUS             RESTARTS   AGE
    audit-pod                                                        1/1     Running            0          111m
    audit-pod-2                                                      0/1     CrashLoopBackOff   26         110m
    audit-pod-3                                                      1/1     Running            0          109m
    ```

    ```sh
    kubectl port-forward pod/audit-pod 5678
    localhost:5678
    > just made some syscalls!
    ```
