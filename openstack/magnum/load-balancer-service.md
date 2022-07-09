---
sidebar_position: 6
---

# LoadBalancer Service
As Deployment resources took care of deployments for us. Service resource will take care of serving the application to connections from outside of the cluster.

#### Requirements:
* You have an account on Cloudify.ro *[Register](https://cloudify.ro/)
* You have created a Cloudify.ro Kubernetes Cluster *[Create Cluster](./create-cluster.md)
* You have downloaded the corresponding kubeconfig file and kubectl is working *[Authentication](./authentication)

### Deploy an Application
The deployment is an alpine-test image.

```shell
$ kubectl apply -f deployment.yml
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hostname-echo-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hostname-echo
  template:
    metadata:
      labels:
        app: hostname-echo
    spec:
      containers:
        - image: "lingxiankong/alpine-test"
          imagePullPolicy: Always
          name: hostname-echo-container
          ports:
            - containerPort: 8080
```
```shell
$ kubectl get deployments            
NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
hostname-echo-deployment   1/1     1            1           41s
```

### Deploy LoadBalancer Service
```shell
$ kubectl apply -f service.yml
```
```yaml
apiVersion: v1
kind: Service
metadata:
  name: hostname-echo-svc
spec:
  ports:
     -  port: 8080
        protocol: TCP
        targetPort: 8080
  selector:
    app: hostname-echo
  type: LoadBalancer

```
The output should show:
```shell
$ kubectl get services
```
```shell
NAME                TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)          AGE
hostname-echo-svc   LoadBalancer   10.254.172.232   <floating-ip>        8080:31521/TCP   5m12s`
```

**In your Cloudify.ro account will be added an Octavia LoadBalancer for Kubernetes LoadBalancer Service and a floating IP for external IP.**

#### Test the connection:
```shell
$ curl <floating-ip>:8080
hostname-echo-deployment-5b4b888787-mmlkg
```



