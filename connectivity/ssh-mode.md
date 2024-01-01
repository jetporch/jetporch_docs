# ▶ SSH

Jet can connect to remote nodes to manage them using parallel SSH connections.

First, see our [CLI documentation](../basics/command-line-usage.md) for more information about launching playbooks, particularly notes about _--batch-size_ (once available) and _--threads_.

Additional details about SSH connections continues below:

### Variable Control

In SSH mode, a playbook will attempt to all connect to all hosts not named "localhost" using SSH, while a node named "localhost" is treated as special, and still uses the [Local](local-mode.md) connection.

The following inventory variables affect connection behavior:



| Inventory Variable                 | Purpose                                                                                                                                                                                       | Default                                                                                                                                                                                                                                             |
| ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| jet\_ssh\_hostname                 | what hostname or IP to connect to for this host                                                                                                                                               | the name of the host in inventory                                                                                                                                                                                                                   |
| jet\_ssh\_port                     | what SSH port to use                                                                                                                                                                          | 22, unless set by "**ssh\_port**" keyword in a Play                                                                                                                                                                                                 |
| jet\_ssh\_user                     | what user to login as                                                                                                                                                                         | the currently logged in username on the control machine, unless set in the [Play](../playbooks/plays.md) with the "**ssh\_user**" keyword or **--user** on the jetp command line                                                                    |
| jet\_ssh\_key\_comment             | if multiple keys are added to SSH agent this can specify a specific one to use versus trying all keys (added in v0.2)                                                                         | the environment variable **JET\_SSH\_KEY\_COMMENT** can also name the default key, or None if absent                                                                                                                                                |
| jet\_ssh\_private\_key\_file       | Specifies the path to a specific keyfile to use instead of using keys from ssh-agent (added in v0.2)                                                                                          | none                                                                                                                                                                                                                                                |
| jet\_ssh\_private\_key\_passphrase | while not wildly secure compared to ssh-agent methods, this allows entering a ssh key unlock passphrase as an inventory variable when specifying jet\_ssh\_private\_key\_file (added in v0.2) | the environment variable **JET\_SSH\_PRIVATE\_KEY\_PASSPHRASE** - this requires all keys use the same default unlock password, see [Secret Management](../playbooks/managing-secrets.md) section for tips about how to access this from other tools |



The parameter jet\_ssh\_key\_comment is for use with **ssh-agent only** for when multiple SSH keys are added to SSH agent and multiple different SSH keys are required to connect to all of the systems being managed.

```
ssh-agent
ssh-add ~/.ssh/id_rsa
ssh-add some_other_key
ssh-add some_other_key2
jetp ssh --inventory ~/private_inventory --playbook playbook1.yml -vvv
```

The parameters jet\_ssh\_private\_key\_file and jet\_ssh\_private\_key\_passphrase work similarly but **bypass ssh\_agent**.  In general, we suggest using SSH agent, though there may be some reasons to avoid it, such as not having ssh-agent plugins for a particular CI/CD system.

It is technically possible (though confusing?) to have some hosts that use SSH agent and others that do not.

