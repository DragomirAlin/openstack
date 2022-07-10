# OpenStack Identity (Keystone)

### Use multiple projects/accounts
1. Create clouds.yaml file (/etc/openstack/clouds.yaml)
```yaml
# This is a clouds.yaml file, which can be used by OpenStack tools as a source
# of configuration on how to connect to a cloud. If this is your only cloud,
# just put this file in ~/.config/openstack/clouds.yaml and tools like
# python-openstackclient will just work with no further config. (You will need
# to add your password to the auth section)
# If you have more than one cloud account, add the cloud entry to the clouds
# section of your existing file and you can refer to them by name with
# OS_CLOUD=openstack or --os-cloud=openstack
clouds:
  openstack:
    auth:
      auth_url: http://10.0.0.10:5000/v3
      application_credential_id: "6d141f23732b498e99db8186136c611b"
      application_credential_secret: "<example secret value>"
    region_name: "regionOne"
    interface: "public"
    identity_api_version: 3
    auth_type: "v3applicationcredential"
```

```bash
 openstack --os-cloud=openstack token issue
 ```

### CLI
Fortunately, we can use automatic command completion in the shell to make things a little easier. To do so, we first need to tell bash about what commands it can auto-complete. Run the following command to do so:

```bash
openstack complete | sudo tee /etc/bash_completion.d/openstack
```

Then, re-initialize tab completion for your shell (this will happen automatically on any subsequent login; you’ll only need to do it manually this one time):
```bash
source /etc/bash_completion
```

You can now use command completion. Try re-running your earlier catalog list command:

```bash
openstack ca<tab> l<tab>
```

#####  ZSH compatibility
ZSH should be totally capable of handling bash completions when it is configured to do so by adding these two lines in ~/.zshrc:

```bash
# Load bash completion functions
autoload -Uz +X compinit && compinit
autoload -Uz +X bashcompinit && bashcompinit
```

#### CLI debug
Now is also a good time to introduce you to the --debug option. Whenever you invoke openstack with --debug, you will see the full callflow between your client library (which the CLI wraps) and the OpenStack API service — or services — it is talking to. Feel free to try it out with any of the Keystone-related subcommands that openstack exposes. For example:
```bash
openstack --debug token issue
```
A particularly interesting thing to do is to expose just the HTTP requests and responses going to and coming from your API services. You can do this with the following bash redirection trick:
```bash
openstack --debug catalog list 2>&1 | grep -E '(REQ|RESP)'
```