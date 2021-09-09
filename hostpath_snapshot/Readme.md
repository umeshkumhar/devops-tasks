## Steps
1) Install hostpath csi driver
2) Create Storageclass, PVC
    ```bash
    $ kubectl apply -f hp_storage.yaml
    $ kubectl apply -f pvc.yaml
    $ kubectl apply -f snapshot.yaml
    ```
    ```bash
    $ kubectl get storageclasses.storage.k8s.io
    $ kubectl get volumesnapshotclasses
    $ kubectl get pvc
    ```

3) Create sample nginx deployment which will be pvc created in last step
    ```
    $ kubectl apply -f nginx-deploy.yaml
    ```

4) Go inside the nginx pod and create the data at mounted path (here /test-pd)
    ```
    $ kubectl exec -it deploy/sample-nginx -- bash
    # echo "sample-nginx-data pod" >> /test-pd/index.html
    ```

5) Now take snapshot of our pvc `csi-pvc` using snapshot.yaml
    ```
    $ kubectl apply -f snapshot.yaml
    $ kubectl get volumesnapshot
    ```

6) Now create new pvc using snapshot created in last step.
    ```
    $ kubectl apply -f preloaded-pvc.yaml
    ```
7) Now create new nginx image which will mount the pvc created in last step

    ```
    $ kubectl apply -f preloaded-nginx.yaml
    $ kubectl exec deploy/preloaded-nginx -- cat somedir/index.html
    ```    
