---
sidebar_position: 5
---

# Persistent Volumes
Kubernetes can create volumes and mount into the containers. Any volume created by kubernetes will be visible in your Cloudify.ro account.
**Note**: persistent volumes created by kubernetes will be billed as any other volume created from your cloud account.

Full documentation on Persistent Volumes in Kubernetes can be found here:
[persistent-volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)

#### Requirements:
* You have an account on Cloudify.ro *[Register](https://cloudify.ro/)
* You have created a Cloudify.ro Kubernetes Cluster *[Create Cluster](./create-cluster)
* You have downloaded the corresponding kubeconfig file and kubectl is working *[Authentication](./authentication)

## Creating Persistent Volumes with CSI Cinder Controller
Before you can create a volume, a StorageClass must be created with Cinder as the provisioner. As usual, the K8s objects are sent to your cluster in YAML format and kubectl apply:

**storageclass.yaml:**
```yaml
# Default NVMe class
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: csi-cinder-nvme
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
provisioner: cinder.csi.openstack.org
allowVolumeExpansion: true
parameters:
  type: NVMe
---
# Secondary HDD class
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: csi-cinder-hdd
provisioner: cinder.csi.openstack.org
allowVolumeExpansion: true
parameters:
  type: HDD
```

Create resource
```shell
$ kubectl apply -f storageclass.yaml
```

Check resource with get and describe
```shell
$ kubectl get storageclass
$ kubectl describe storageclass csi-cinder-nvme
```

Create a Persistent Volume Claim (PVC), you can create a new volume with the help of a Persistent Volume Claim, Persistent Volume is created dynamically by the CSI Cinder Controller.

**pvc.yaml:**
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nginx-pvc
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

### Create a deployment and a service
The deployment is a nginx server for testing purposes.

**deplyment.yaml:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
        volumeMounts:
          - mountPath: /usr/share/nginx/html
            name: shared
      volumes:
      - name: shared
        persistentVolumeClaim:
          claimName: nginx-pvc
          readOnly: false

---

apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
spec:
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
      name: http
  selector:
    app: nginx
  type: LoadBalancer
```

Apply resources
```shell
$ kubectl apply -f deployment.yaml
```

Check resources
```shell
$ kubectl get deployments
```

Access nginx terminal:
```shell
$ k exec -it deployment/nginx bash
```

Add content to html page from nginx terminal:
```shell
$ echo "Hello world!" > /usr/share/nginx/html/index.html
```
Kubernetes and the CSI Cinder Controller naturally ensure that your new volume and the associated pods are always started at the same worker node.

