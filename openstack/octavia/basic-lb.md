# Examples
## Deploy a basic HTTP load balancer
While this is technically the simplest complete load balancing solution that can be deployed, we recommend deploying HTTP load balancers with a health monitor to ensure back-end member availability. See Deploy a basic HTTP load balancer with a health monitor below.

#### Scenario description:

Back-end servers 192.0.2.10 and 192.0.2.11 on subnet private-subnet have been configured with an HTTP application on TCP port 80.
Subnet public-subnet is a shared external subnet created by the cloud operator which is reachable from the internet.
We want to configure a basic load balancer that is accessible from the internet, which distributes web requests to the back-end servers.

#### Solution:

1. Create load balancer lb1 on subnet public-subnet.
2. Create listener listener1.
3. Create pool pool1 as listener1’s default pool.
4. Add members 192.0.2.10 and 192.0.2.11 on private-subnet to pool1.
   
CLI commands:

```
openstack loadbalancer create --name lb1 --vip-subnet-id public-subnet
# Re-run the following until lb1 shows ACTIVE and ONLINE statuses:
openstack loadbalancer show lb1
openstack loadbalancer listener create --name listener1 --protocol HTTP --protocol-port 80 lb1
openstack loadbalancer pool create --name pool1 --lb-algorithm ROUND_ROBIN --listener listener1 --protocol HTTP
openstack loadbalancer member create --subnet-id private-subnet --address 192.0.2.10 --protocol-port 80 pool1
openstack loadbalancer member create --subnet-id private-subnet --address 192.0.2.11 --protocol-port 80 pool1
```

Create a health monitor on pool1 which tests the “/healthcheck” path.
The route /healthcheck must response with 200 to instance
```
openstack loadbalancer healthmonitor create --delay 5 --max-retries 4 --timeout 10 --type HTTP --url-path /healthcheck pool1
```

Just PING
```
openstack loadbalancer healthmonitor create --delay 5 --max-retries 4 --timeout 10 --type PING pool1
```

Create a fully populed load balancer
https://docs.openstack.org/api-ref/load-balancer/v2/?expanded=create-a-load-balancer-detail#creating-a-fully-populated-load-balancer
