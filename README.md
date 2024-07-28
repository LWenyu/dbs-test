# dbs-test

## 1. Set up cluster
Cluster is set up using Red Hat Demo

## 2. Install ArgoCD
- Install community argoCD operator
- Create `argocd-platform` projects, switch to that project.
- Create ArgoCD instance (take the default configurations), name as `argocd`

## 3. Create Git Repo containing application

### Containerized Application using Python Flask library
Login to the quay and push the images to the quay registry


```shell
$ podman login -u weluo quay.io
$ podman build . -t quay.io/weluo/hello-python:v1.0
$ podman push quay.io/weluo/hello-python:v1.0
```

Test image
```shell
$ oc new-app --name hello-python quay.io/weluo/hello-python:v1.0
$ oc create route edge hello-world --service hello-world
$ curl -kv https://hello-world.apps.example.com
```

## 4. Configure ArgoCD
Retrieve 'admin' password from secret
```shell
$ oc extract secret/argocd-cluster --to -
# admin.password

``` 

Open the browser and point the URL to the route, use `admin` and the `password` above to login.

## Argocd Binary
Download the argocd binary in bastion

```shell
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64
```

## Adding Cluster
Add the cluster using 'argocd' CLI 
First login to bastion, test the login to the argocd-server
```shell
$ argocd login argocd-sample-server-argocd-platform.apps.cluster-dqk9j.dqk9j.sandbox963.opentlc.com:443
Username: XXXX
Password: XXXX
'admin:login' logged in successfully
Context 'argocd-platform.apps.cluster-lplz5.lplz5.sandbox1583.opentlc.com:443' updated
```
Add the cluster after authentication
```shell
$ oc config current-context
$ argocd cluster add argocd-platform/api-cluster-lplz5.lplz5.sandbox1583.opentlc.com:6443/system:admin
WARNING: This will create a service account `argocd-manager` on the cluster referenced by context `argocd-platform/api-cluster-lplz5.lplz5.sandbox1583.opentlc.com:6443/system:admin` with full cluster level privileges. Do you want to continue [y/N]? y
INFO[0001] ServiceAccount "argocd-manager" created in namespace "kube-system"
INFO[0001] ClusterRole "argocd-manager-role" created
INFO[0001] ClusterRoleBinding "argocd-manager-role-binding" created
INFO[0002] Created bearer token secret for ServiceAccount "argocd-manager"
WARN[0002] Failed to invoke grpc call. Use flag --grpc-web in grpc calls. To avoid this warning message, use flag --grpc-web.
Cluster 'https://api.cluster-lplz5.lplz5.sandbox1583.opentlc.com:6443' added
```
Check in the argoCD if the new cluster has been added.

## ArgoCD Configuration
- Add cluster (already done)
- Add projects (use GUI)
- Add repository (use GUI)
- Add application

### Create namespace in OpenShift
```shell
$ oc new-project argocd-dev
$ oc new-project argocd-prod
```
Then create application for Dev and Prod namespace.
Verify if the pods are running in the respective namespaes.




