---
sidebar_position: 3
---
# Authentication

#### Requirements
* [Deploy a Cluster](./create-cluster)
* [Kubectl](https://kubernetes.io/docs/tasks/tools/)


### Download Client Keystone Auth
Client Keystone Auth is a plugin for kubectl. It allows kubectl to get an auth token from Keystone and use that to authenticate to the Kubernetes api server.

[Click for download](https://dl.cloudify.ro/cloud-provider-openstack-v1.22.0-linux-amd64/client-keystone-auth)

### Generate kubeconfig
In the cluster view page of Cloudify.ro Dashboard you can generate Kubeconfig, after clicking `Generate kubeconfig`, you need to download the kubectl.yaml file in the same directory as Client Keystone Auth.

Once kubeconfig is generated, it will generate application credentials for authentification to the Kubernetes API, 
if you delete the application credentials generated for kubeconfig, authentification to the Kubernetes API will no longer work.

### Connect to the Cluster
`kubectl` a configuration file and an authentification plugin to connect to Cloudify.ro
All these configuration will be placed in the default home directory `~/.kube`.

```shell
# Make the kubectl home directory
mkdir -p ~/.kube

# Download client-keystone-auth plugin
curl -o ~/.kube/client-keystone-auth https://dl.cloudify.ro/cloud-provider-openstack-v1.22.0-linux-amd64/client-keystone-auth
# Make sure the binary is executable
chmod +x ~/.kube/client-keystone-auth

# Create the kube config file
# Replace ~/Downloads with the folder where you downloaded
# the kubeconfig from the previous step
mv ~/Downloads/kubeconfig.yaml ~/.kube/config
```

Run get nodes to test the connection.
```shell
kubectl get nodes
```
```shell
NAME                                    STATUS   ROLES    AGE    VERSION
k8s-cluster-001-nkmeoqkvhesi-master-0   Ready    master   2d1h   v1.21.1
k8s-cluster-001-nkmeoqkvhesi-master-1   Ready    master   2d1h   v1.21.1
k8s-cluster-001-nkmeoqkvhesi-master-2   Ready    master   2d1h   v1.21.1
k8s-cluster-001-nkmeoqkvhesi-node-0     Ready    <none>   2d1h   v1.21.1
k8s-cluster-001-nkmeoqkvhesi-node-1     Ready    <none>   2d1h   v1.21.1
k8s-cluster-001-nkmeoqkvhesi-node-2     Ready    <none>   2d1h   v1.21.1
```

### References
[Keystone Auth](https://github.com/kubernetes/cloud-provider-openstack/blob/master/docs/keystone-auth/using-client-keystone-auth.md)