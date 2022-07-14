# Key Manager - Barbican

Examples of secrets you can store in Barbican include:

- private keys of asymmetric public/private key pairs, such as SSH private keys
- symmetric keys, such as LUKS decryption secrets for block devices
- x.509 certificates and private keys, such as those used in SSL/TLS encryption for encrypted web services

You can use Barbican secrets in a number of ways, depending on the OpenStack service you are using:

### Cinder
Cinder comes with the option of encrypting persistent volumes for Linux VMs, using Linux Unified Key Setup (LUKS) volumes. When using this option, Cinder stores the LUKS decryption secret — a symmetric encryption key — in Barbican, and uses dm-crypt to encrypt volume data.

### Octavia
Octavia has the option to define TERMINATED_HTTPS listeners that speak the HTTPS protocol to clients, and unencrypted HTTP to the backend servers. In that configuration, Octavia load balancers serve as the endpoint of the HTTPS connection with the client, and as such must have access to TLS certificates and private keys. Both of these are stored in Barbican.

### Heat
Heat has the OS::Barbican::Secret resource type, which (in addition to storing Cinder encryption secrets and Octavia certificate information) can also store arbitrary, generic secret data. You can then pass that data into other configuration items and might, for example, automate configuring a database client with the proper password. This will keep you from having to store such sensitive data in your Heat template — although Heat itself makes no guarantees about the integrity of your secret (it may still be shared with another resource, or an output, unencrypted).