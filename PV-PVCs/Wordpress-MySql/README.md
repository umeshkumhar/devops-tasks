# Example: Deploying WordPress & MySql With PV

## Steps

- Create a `kustomization.yaml` with
    - a Secret generator
    - MySql resource configs
    - WordPress resource configs
- Apply the kustomization directory by `kubectl apply -k ./`
- Clean up using `kubectl delete -k ./`

### Create kustomization.yaml

#### Add a Secret generator

- **Note:** Change `YOUR_PASSWORD` with actual Password

```
cat <<EOF >./kustomization.yaml
secretGenerator:
- name: mysql-pass
  literals:
  - password=YOUR_PASSWORD
EOF
```

#### Add WordPress & MySql resources

```
cat <<EOF >>./kustomization.yaml
resources:
  - mysql-service.yaml
  - mysql-pvc.yaml
  - mysql-deployment.yaml
  - wordpress-service.yaml
  - wordpress-pvc.yaml
  - wordpress-deployment.yaml
EOF
```
