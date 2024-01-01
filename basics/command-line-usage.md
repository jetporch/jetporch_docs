# ✈ Command Line Usage

We use the jetp CLI to apply configurations or send orders to remote machines, often like this:

```
$ ssh-agent
$ ssh-add ~/.ssh/id_rsa
$ jetp ssh --playbook ~/playbooks/setup.yml --inventory ~/inventory --user opsteam
```

Local configuration looks like this:

```
$ jetp local --playbook ~/local_playbooks/local.yml
```

Built-in help is available and will give you a better idea of what jet can do:

```
$ jetp --help
```

Details on available subcommands and options continue below.

## Modes

Jetp has various modes grouped into subcommands.&#x20;

This subcommand system selects how we wish to communicate with our managed machines -- [local](../connectivity/local-mode.md), [ssh](../connectivity/ssh-mode.md), and so on. There are also a few other options.

The main command for configuring local machines is "j_etp local_", and if you want to push orders out to multiple remote machines there is "_jetp ssh_'. &#x20;

{% hint style="info" %}
**Upcoming Connectivity Modes**

Jet is already fairly effectient and can easily use very high thread counts (such as 100-150 simultaneous threads) for parallel SSH.  To push this over the edge, future "[planetary scale](../connectivity/agent-mode.md)" and [SSH fanout ](broken-reference)deployment modes will be available using additional subcommands in the future.
{% endhint %}



| subcommand     | purpose                                                                                                   | makes changes |
| -------------- | --------------------------------------------------------------------------------------------------------- | ------------- |
| check-ssh      | connects to **remote** machines over SSH and sees if any changes would  be made if running in 'ssh' mode. | no            |
| ssh            | connects to **remote** machines and runs playbooks in a 'live' configuring mode                           | **yes**       |
| check-local    | evaluates a playbook locally and reports if any changes would be made if running in 'local' mode          | no            |
| local          | runs a playbook locally in 'live' configuring mode                                                        | **yes**       |
| show-inventory | display information about SSH inventory groups and hosts                                                  | no            |



{% hint style="info" %}
**About Check Modes**

'check-ssh' and 'check-local' modes compare hosts to a define policy rather than configure them.

These are sometimes imperfect in that they do not evaluate any shell tasks that might be relied upon for conditional execution later on in a playbook, if using conditonal expressions in the playbook. They _do_ remotely evaluate [facts](../modules/control-flow.md) and other passive tasks.&#x20;

It is possible that a check-local or check-ssh command would fail when a 'local' or 'ssh' run would succeed. If this happens, do not be dissuaded, Jet is designed for declarative application of configurations, and this is mostly provided for when someone would want to use automation as a policy monitoring tool.

There is no need to run check-mode before applying configurations.
{% endhint %}

## Primary Parameters

The following parameters are used in nearly all jetp runs:

### Playbook Path (--playbook, -p)

```
-p path/to/playbook.yml

--playbook path/to/playbook.yml

--playbook pb1.yml
```

The [playbook](../playbooks/playbook-overview.md) tells Jet what actions to perform (or what policy to apply) on what machines.  This is needed for both local and SSH configuration.

### Inventory Path (--inventory, -i)

```
-i path/to/inventory

--inventory path/to/inventory/

--inventory dynamic_inventory_script.py

--inventory path/one/:path/two/

--inventory path/one --inventory path/two
```

When using SSH modes, and only then, we must specify an [Inventory](broken-reference). This inventory defines what machines can be addressed and configured. The playbook narrows this down further by choosing [groups](../inventory/groups.md) in that inventory.

For file-based[ static inventory,](../inventory/file-based-inventory.md) we specifiy the _directory name_ for the inventory. For a [dynamic inventory](../inventory/dynamic-cloud-inventory.md), we give the inventory script _filename_.&#x20;

{% hint style="info" %}
**Starting With Static Inventory**

We recommend using a file based inventory using some known hostnames or IP addresses when learning Jet, and once you have done something sufficiently "hello-world" like, then exploring the world of dynamic inventory sources. Starting with dynamic inventory is ok, but it's probably more productive to explore the language first and get to that a bit later.
{% endhint %}

Using multiple simultaneous inventory sources is possible, enabling, for example, communication with multiple clouds at the same time, or mixing clouds with physical datacenters.

{% hint style="info" %}
**The Inventory Flag Is Not Always Required**

The pending [messaging-enabled modes](../connectivity/agent-mode.md) will not use an --inventory parameter, nor does [local mode](../connectivity/local-mode.md) use it.
{% endhint %}

### Roles Path (--roles, -r)

```
-r path/to/roles/directory

--roles path/to/roles/directory

--roles path/to/roles/directory:path/to/more/roles

```

[Roles](../playbooks/roles.md) are reusable bundles of orders, desired state configurations, automation content, or other work that we wish to apply to [groups](../inventory/groups.md) using playbooks.  Once users understand the basics of Jet, we expect nearly all Jet automation projects will make heavy usage of roles.

Jet will by default look for roles in a subdirectory called "roles/" alongside the playbook, and this parameter adds additional directories to the roles search path.  Roles alongside the playbook would only be used in a single project, whereas a roles path might indicate roles tracked in another git repository. Having an git repo full of common roles that service-specific playbooks can use may be a useful pattern to emulate.

Jet will always also look in the 'roles/' directory alongside the playbook _after_ these locations are searched.

{% hint style="info" %}
**Specifying Roles From Environment Variables**

The environment variable JET\_ROLES\_PATH can also set additional roles path search locations.  It works just like the Unix parameter PATH and can contain multiple directory values seperated by colons.
{% endhint %}

## Additional Features

The following parameters are good to know, and are situationally relevant. You may not use all of these parameters, and it's fine to not try them until they are needed. If you're just getting a feel of learning Jet, you're welcome to skip ahead in the documentation and skim these later.

### Ask Login Password (--ask-login-password)

This is _NOT_ recommended and is provided for limited bootstrapping capabilities only - namely adding your SSH key into authorized\_keys in a home lab setup.

We strongly recommend using ssh-agent with jet because passing login passwords to compromised systems can share your login password with an untrustworthy host. This option is only here use with bootstrapping existing non-cloud lab systems. Be careful with what machines you talk to this way. For cloud based systems, you can almost always provision the system with your SSH key preinstalled.

```
--ask-login-password
```

Also note, the --ask-login-password parameter will _not_ work with modules that support the --forward-agent parameter (namely the git module when doing private repo operations)

### Default SSH User (--user)

```
-u stuart

--user bob
```

In [SSH](../connectivity/ssh-mode.md) mode, jetp's default behavior is to attempt to connect to the remote machine at the given hostnames as specified in inventory, on port 22, and use the username of the currently logged in account ($USER) on the control machine.&#x20;

Most commonly, _only_ the remote username needs to be changed, and this can be adjusted on the command line:

```
jetp --playbook pb1.yml --inventory ~/inventory --user opsteam
```

In other words, if you were configuring from a Mac and your Mac account is logged in as "fezzik" and your SSH keys are for accounts on your production machines named "prod", you would want to pass "--user prod" on the command line.&#x20;

The remote user (as well as the port and the actual dns name or IP used for the host) can also be set using inventory variables, [as described here](../connectivity/ssh-mode.md), or set in the [Play](../playbooks/plays.md).&#x20;

### Extra Variables (--extra-vars, -e)

Often most of a playbook will be checked into git and inventory will be prepared, but some variables need to be injected from outside.  A most common example of this would be if making a "release" playbook where perhaps some version information came from a build system or another file.

To enable this use case, --extra-vars supplies additional [variables](../playbooks/using-variables.md) to Jet either on the command line or from arbitrary YAML files.&#x20;

Inline JSON is assumed unless the argument starts with "@", in which case any file path can be used for YAML input.

Extra variables have the same level of precedence as the "vars" or "vars\_files" keyword in a playbook, which means the values will definitely override any defaults or values set in inventory.

```bash
jetp --playbook release.yaml --inventory ~/inventory 
     --extra-vars '{"version": "1.23"}'      

jetp --playbook release.yml --inventory ~/inventory --extra-vars @version.yml

```

### Limits (--limit-hosts, --limit-groups)

```

--limit-hosts www.example.com:www2.example.com

--limit-groups group1:group2
```

Normally the playbook says what groups to configure or execute tasks against. If a limit _(either --limit-hosts or --limit-groups)_ is specified, it is possible to further restrict the execution to a specified subset of hosts or groups, narrowing the playbook down further.

If more than one item is specified by separating the terms with a ":", the two are treated as an OR condition, where the hosts can be in either of the two groups.

This can be useful in debugging a playbook, or re-running a playbook on just a particular host that has failed.  It can also be used to make fairly generic playbooks (such as, for, maybe restarting a specific service or doing a migration) that can be sent to a specific node or cluster without editing the playbook files in version control.

### SSH Forwarding (--forward-agent)

SSH agent forwarding currently only has to be used when using the git module when using key-based authentication against private repositories.&#x20;

The security implications of SSH forwarding for this module are discussed in the git module documentation.&#x20;

Agent forwarding is only used during operation of module operations that request it (such as the git module when using non-https:// checkouts), so all modules will otherwise work fine with SSH Agent Forwarding disabled. For instance, SSH agent forwarding is not (yet) made available as an option to the _shell_ module.

```
--forward-agent
```

### Sudo (--sudo)

Administrators should already be familiar with sudo.

The need to sudo after login can be specified in a playbook or on the command line. If sudo details are set at [playbook level](../playbooks/plays.md#sudo), the command line setting will _override_ the playbook.

Per [task](../playbooks/tasks-and-task-modifiers.md#sudo) sudo settings then override all of these settings, as even when sudoed to, for example, the root account, some automation steps may need to be run from other user accounts, such as a database account.

```
--sudo root
```

{% hint style="info" %}
**Alternative sudo replacements**

The sudo command does not mean the literal 'sudo' command needs to be used, and other sudo replacement tools are supported.  See the [Play](../playbooks/plays.md) documentation on the _sudo\_template_ keyword for how to configure sudo alternatives.
{% endhint %}

{% hint style="info" %}
**Sudo With Password**

Passwords (and configurable password prompts) are not yet supported in Tech Preview 1. Sudo passwords are going to be added as a feature in the Tech Preview 2 release.
{% endhint %}

### Tags (--tags)

Sometimes users may want to execute only a portion of a playbook.  This may be to save time in re-running a configuration after some error has been corrected, or when authoring playbook content for the first time.

If --tags is specified, only the subset roles and tasks with the given tag are executed.  How tags are applied in playbooks is explained in the [Play](../playbooks/plays.md) and [Task](../playbooks/tasks-and-task-modifiers.md) chapters.

Multiple tags can be specified using the ":" seperator, in which case the task only needs to have one of the given tags to run.

```
--tags foo_app
--tags security:common
```

### Threads (--threads, -t)

```
-t 100

--threads 100
```

In SSH modes, this setting sets how many parallel threads are used to talk to remote systems. These are OS _threads_, not forks, so the value can easily go quite high depending on the capabilities of the control machine.

{% hint style="info" %}
**Setting Thread Defaults**

This value can also be set by the environment variable JET\_THREADS. &#x20;
{% endhint %}

### Verbosity (-v, -vv, -vvv)

Verbosity levels can help debug a playbook, but having too much output can sometimes be a bit much to handle.

Verbosity levels can be adjusted by adding -v parameters, as many as you would like!

```
jetp ssh --playbook pb1.yml --inventory ~/private_inventory -v
jetp ssh --playbook pb1.yml --inventory ~/private_inventory -vv
jetp ssh --playbook pb1.yml --inventory ~/private_inventory -vvv

OR

jetp ssh --playbook pb1.yml --inventory ~/private_inventory -v -v -v
```

The levels are roughly as follows:

1. show all commands executed by the shell and when file transfers happen. This may give you some idea about what modules are doing internally and could be interesting to many users, even if it does not represent actionable information.
2. reserved
3. show the output of all commands run.  This could be a lot of output and is often best used with --limit-hosts singlehost.example.com for debugging purposes.

When debugging playbook problems, verbosity settings work well with --limit-groups or --limit-hosts to retry a playbook with more output on a subsset of the total number of systems.

Verbosity flags are not required to show any detail about failures that result in the failure of a module call, these are shown automatically.

### Help (--help)

\--help prints a friendly cheat sheet showing modes, long, and short options:

```
jetp --help
```



