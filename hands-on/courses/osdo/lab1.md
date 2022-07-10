# Lab 1 - Introduction to OpenStack

When running this setup on your own hardware, it's ultimately your responsibility to configure each node's physical network interfaces. Most automated OpenStack deployment tools, including the one you'll use here, will not do it for you: they have to be told about existing network configuration.


### OpenStack and Ansible
In these exercises, you'll use Ansible to deploy OpenStack onto Ubuntu machines. Ansible is a configuration management and provisioning tool that can be used, among other things, to rapidly deploy OpenStack. Ansible relies on user-created playbooks, written in YAML, for orchestration. In particular, you will use playbooks from the OpenStack Ansible project, from now onwards referred to simply as OSA.


```yaml
cidr_networks:
  container: 192.168.122.0/24
  tunnel: 192.168.133.0/24
  storage: 192.168.155.0/24
```

This tells OSA that the OpenStack container network should use the pre-existing 192.168.122.0/24 range: it is the control plane for infrastructure services, OpenStack APIs, and Horizon. The tunnel network, set to use 192.168.133.0/24, is the data plane for OpenStack tenant VXLAN networks. The storage network, on 192.168.155.0/24, is the data plane for services such as Cinder and Swift.