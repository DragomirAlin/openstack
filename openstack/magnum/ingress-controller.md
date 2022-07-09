---
sidebar_position: 7
---

# Ingress Controller

## What is an Ingress Controller?
In Kubernetes, Ingress allows external users and client applications access to HTTP services. Ingress consists of two components.

- Ingress Resource is a collection of rules for the inbound traffic to reach Services. These are Layer 7 (L7) rules that allow hostnames (and optionally paths) to be directed to specific Services in Kubernetes.
- Ingress Controller which acts upon the rules set by the Ingress Resource, typically via an HTTP or L7 load balancer.

It is vital that both pieces are properly configured to route traffic from an outside client to a Kubernetes Service.

### Expose an Application with NGINX Ingress Controller
#### Requirements:
* You have an account on Cloudify.ro *[Register](https://cloudify.ro/)
* You have created a Cloudify.ro Kubernetes Cluster *[Create Cluster](./create-cluster)
* You have downloaded the corresponding kubeconfig file and kubectl is working *[Authentication](./authentication)

#### Deploy NGINX Ingress Controller
We need to deploy the ingress controller with the following command:
```shell
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.0.5/deploy/static/provider/cloud/deploy.yaml
```

#### Pre-flight check
A few pods should start in the ingress-nginx namespace:
```shell
$ kubectl get pods --namespace=ingress-nginx
```

A Load Balancer service with external ip has been created
```shell
$ kubectl get svc --namespace=ingress-nginx
```
```shell
NAME                                         TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                      AGE
service/ingress-nginx-controller             LoadBalancer   10.254.1.54     <floating-ip>   80:30498/TCP,443:30899/TCP   36m
service/ingress-nginx-controller-admission   ClusterIP      10.254.107.18   <none>          443/TCP                      36m
```

In your Cloudify.ro account will be added an Octavia LoadBalancer for Ingress NGINX Controller Service and a floating IP for external IP


#### Deploy Application
We will use a test application called cafe-ingress, you can find it at this URL. It’s a pretty simple application just serving different web pages depending on the URL you type.
```shell
$ kubectl create -f https://raw.githubusercontent.com/nginxinc/kubernetes-ingress/master/examples/complete-example/cafe.yaml
```

#### Create Ingress Resource
Create the ingress object with this YAML manifest.
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: cafe-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
spec:
  rules:
    - http:
        paths:
          - path: /tea
            pathType: Prefix
            backend:
              service:
                name: tea-svc
                port:
                  number: 80
          - path: /coffee
            pathType: Prefix
            backend:
              service:
                name: coffee-svc
                port:
                  number: 80
```
```shell
$ kubectl create -f cafe-ingress.yaml
$ kubectl get ing
```

You can test that this ingress is configured correctly by accessing your test application:
```shell
$ curl http://<floating-ip>/tea
$ curl http://<floating-ip>/coffee
```

The result should show:
```shell
Server address: <address>
Server name: <server-name>
Date: <date>
URI: /tea or /coffee
Request ID: <request-id>
```


