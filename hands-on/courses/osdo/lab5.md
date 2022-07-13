# Nova

### nova-api
nova-api exposes an interface to Nova, not only to users but to all other services running within the cloud.

### nova-compute
nova-compute starts and stops virtual machines on virtualization nodes. When Nova's scheduler selects a host for a VM, the nova-compute instance running on that host will get the VM up and running. Consequently, nova-compute has to run on every virtualization node of your OpenStack cloud.

### nova-scheduler
nova-scheduler is responsible for "procurement" within your cloud. It knows which resources are available and what load every virtualization node is under, and so can decide where new VMs are launched. Due to this centralized nature of the service, it will typically run only once in your cloud (as opposed to nova-compute, which will run on all hypervisor nodes).

### nova-novncproxy
nova-novncproxy is an HTTP-capable proxy server makes your VM's console available through the dashboard. It can be reached via HTTP and forwards the VNC connection to a port of the KVM domain.

While the VNC server itself is intrinsic to libvirt (and thus runs on every virtualization node), the VNC proxy itself typically runs on an API node, or several API nodes behind a load balancer.

Somewhat confusingly, however, the base URL for the VNC proxy must be configured on every compute node.

### nova-conductor
nova-conductor is a database abstraction layer written specifically for nova-compute: it permits all compute instances to function without direct access to the central database.

