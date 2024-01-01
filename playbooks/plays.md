# 🏃♂ Plays

As described earlier, a playbook is a YAML list of one or more Play structures.  It must be a list, you _cannot_ have a file that has a playbook loose by itself, but a list of just one play is fine.

_Plays_ assign work (or configurations) to [Groups](../inventory/groups.md) from [Inventory](broken-reference) in the form of [Tasks](tasks-and-task-modifiers.md) and [Roles](roles.md). &#x20;

This is all best understood while looking at our [example repo](../basics/example-content.md).

{% hint style="info" %}
**What's In A Name?**

A common phrase in IT is often the word "runbook", here playbook is meant - as Michael originally used it in _Ansible_ - as a kind of sports analogy, there are multiple different plays a team could run for different game-time situations. In the end, it's still the same concept, just meant to be slightly more fun. Beyond this, Jet mostly rejects metaphors, themes, and such, which is why you won't see anything called "landing gear" or "fuselage" or anything like that. _Ansible is a registered Trademark of Red Hat Inc._
{% endhint %}

## Playbook Syntax

A basic playbook file might looks like the file below.&#x20;

The following example selects two groups from inventory (in the case of [SSH](../connectivity/ssh-mode.md)) and applies two different tasks to any host machine that is included in either group. &#x20;

{% hint style="info" %}
**When Is Inventory Needed?**

[Local configuration mode](../connectivity/local-mode.md) does not use inventory, local playbooks should be written targetting the 'all' group.  [Planetary Scale](../connectivity/agent-mode.md) features will also not need inventory.
{% endhint %}



```
# playbook1.yml
# not using roles

- name: sample playbook

  groups:
     - groupA
     - groupB
  
  tasks:
  
     - !shell
       cmd: echo hi
  
     - !dnf 
       package: tree
  
```

[Task](plays.md#tasks) sections are full of invocations of [Modules](broken-reference). Playbooks can contain loose tasks, tasks loaded in from roles, or both.

As mentioned before, most users should consider organizing tasks into [Roles](roles.md) to better manage their configuration policies as they grow and encompass more and more projects or services.

{% hint style="info" %}
**Roles Vs Loose Tasks: What Executes First?**

If loose tasks and roles exist in the same playbook, roles will be executed first.
{% endhint %}

Having a playbook with no loose tasks (one that just uses roles) can also be _much_ cleaner to look at, so you may find yourself preferring this approach once learning the program:

```
# playbook2.yml
# just applying some roles

- name: roles demo
  groups:

     - groupA
     - groupB
  
  roles:
   
      - role: common 
      - { role: example_app_two, vars: { port: 3005, sword: 1, battlecat: 1 } }
                 
```

Each play runs in order, and a total failure of all hosts in one play will stop the execution of the playbook.

```
# playbook3.yml
# deploy a multi-tier application using roles

- name: caching tier
  groups:
     - caching
  roles:
     - role: common
     - role: caching_layer
     - role: security

- name: database tier
  groups:
     - database
  roles:
     - role: common
     - role: database_setup
     - role: security
 
- name: webserver tier
  groups:
     - webservers
  roles:
     - role: common
     - role: webservers
     - role: security
     
 
     
```

When a set of playbooks completes (or if it fails), Jet will output a nice summary of statistics about the playbook run.

Ok, so we're about done going over the play structure!

There are other optional keywords that can be used in plays, including the ability to declare  variables either directly in the playbook or using YAML files on disk. The following example shows everything we can set at play level. Below is an arbitrary example of are all the playbook keywords you _can_ use, though having to use them all together would be somewhat unusual:

```
# playbook3.yml
# all the options together in a very unrealistic example

- name: too many options, why would you do this

  groups: [ 'groupA', 'groupB' ]
  ssh_user: opsteam
  ssh_port: 22

  # variable section
  defaults:
     some_value: 1000
  vars:
     other_value: 8675309
  vars_files:
     - vars.yml
  
  roles:
     - { role: alpha, tags: [ 'alpha' ] }
     - { role: beta,  tags: [ 'beta' ]  } 
     - { role: gamma, vars: { x: 1234, y: 'asdf' }, tags: ['gamma'] }
  
  tasks:
    
     - !template
       src: foo.conf.hb
       dest: /etc/foo/foo.conf
       and:
           notify: restart foo    
         
  handlers:
  
     - !sd_service
       service: foo
       restart: true
       with:
           subscribe: restart foo
 
```

The keywords in a play, shown above, are:

### defaults

Sets default variables at a very low level of precedence that are easily overriden by both inventory variables and role parameters.

See [Variable Precedence](using-variables.md)

### groups

Specifies what groups of hosts to communicate with. Unlike the other parameters on a play, this parameter is _required_.  For local playbooks, the group name should be set to 'all'.

Group names must be matched exactly for them to be targetted.  Specifying the name of a group that is not in inventory will intentionally cause an error.

### handlers

Handlers are tasks that only run when certain resources are notified, and are mainly used for restarting stateful services (such as systemd services). This is described in a more detail in the [Handlers](plays.md#handlers) chapter.

### name

The playbook must have a name which gives an explanation of what the play is about to do.  This is shown in the output and helps show what is going on when multiple plays exist in the same playbook

### roles

if specified, adds [roles](roles.md) to apply to the groups.  See the [CLI Instructions](../basics/command-line-usage.md) for how to configure the roles search path.

A 'roles/' directory parallel to the playbook file is automatically included in the roles search path.



```
- name: demo of role inclusion
  groups: 
     - all
     
  roles:
     - { role: example }
     - { role: example2, vars: { x: 5000, y: "foo" } }
     - { role: example3, tags: ['example3'] }

  
  tasks:
     # ...
```

The 'vars' keyword inside the role invocation allows roles to be parameterized.  This means a role could be developed for common usage in many different playbooks, and used slightly differently in each playbook depending on the vars passed to it. For instance, a role may represent a customer installation of a particular piece of software (passing in a customer ID).

Roles can also be _tagged_ like tasks, in which case the given tag is applied to all tasks within the role.  See [Tasks](tasks-and-task-modifiers.md) for more information on the tag feature and how to use it.

### sudo

If --sudo username is not specified on the command line, this playbook setting will cause all tasks in the play to _sudo_ to the given username _unless_ a  "sudo" override [is set on a specific task](tasks-and-task-modifiers.md#sudo) to pick a different user.

```
- name: demo of sudo config 
  
  groups: 
     - all
     
  sudo: root
  
  tasks:
     - ...
     
```



{% hint style="info" %}
**Dealing With Sudo Passwords**

to use the sudo feature, sudoers rights on the remote system should _currently_ be configured to allow _NOPASSWD_ access on any automation accounts as the sudo command does not support interactive password prompts at this time. Support for sudo passwords will be added in the near future.  Since sudo passwords are transmitted to remote clients, they don't really represent a strong security feature, but we recognize many users have these enabled per organizational policies.
{% endhint %}

{% hint style="info" %}
**Sudo Refinement Options**

Because jet works via the shell, it is still possible to finely control to restrict what specific shell commands a user can run via jet over sudo via /etc/sudoers, but this is _somewhat_ misleading, once package installation (apt, dnf, etc) is allowed, these commands can do basically anything. Providing sudoers access to run all commands is a suggestion, but either method works as long as the limitations are understood.
{% endhint %}

### sudo\_template

By default Jet will use 'sudo' to interpret 'sudo' directives, but other similar commands for alternative sudo tools are supported! The optional parameter sudo\_template on a playbook configures this behavior:

```
- name: showing sudo template syntax
  groups:
    - databases
    - webservers
    
  sudo: root
  sudo_template: "/usr/bin/sudo -u '{{jet_sudo_user}}' {{jet_command}}"
    
  tasks:
     # ...ssh_user / ssh_port
```

It is important to note these variables in the sudo template line are not "normal" variables in jet, and variables other than jet\_sudo\_user and jet\_command _cannot_ be used inside sudo\_template keyword value. Because these are not really normal variables, it is also intentionally impossible to programmatically set these variables anywhere in the program, including inventory.



{% hint style="info" %}
**Sudo Replacements and Passwords**

As per the above section explaining _sudo_, responding to a sudo-like command with a password with these templates is  not currently supported but may be implemented later.
{% endhint %}



When using sudo\_template, be sure to fully path the address of the setuid program to ensure you are executing the right program.

### ssh\_user / ssh\_port

For SSH-based deployment cases, connections default to the current logged in username on the control machine and port 22.

The username can be overriden by --user on the command line, and both the user and port by specific parameters in inventory variables, as described in [SSH](../connectivity/ssh-mode.md). &#x20;

For situations where all hosts share a common user and/or port, a value for all hosts can also be specified in the playbook. For instance, this might be used if all machines used port 5222 for SSH, or all users logged in with the name "opsteam".  Both of these parameters are optional.

```
- name: showing ssh user/port global config
  groups: 
     - all
     
  ssh_user: opsteam
  ssh_port: 5222
  
  tasks:
     # ...
```

### tasks

If specified, lists any free [tasks](tasks-and-task-modifiers.md) that are not defined in roles. Tasks are fundamental to doing anything with Jet, so consult the example content if you do not understand them after reading the task section and module documentation.

Users will usually start learning Jet with tasks and progress to roles, but it's fine for one-off and simple playbooks to use tasks without adopting roles. Loose tasks are not necessarily a mark of a "wrong" playbook, but rather one that did not need to have content reused elsewhere.

### vars

Sets some additional [variable](using-variables.md) values that can be used in playbooks and templates, that will  override any other variable definition. &#x20;

While it is up to you, we suggest using this feature very sparingly and using _defaults_ instead, which allows inventory variables to have some override ability.  Use vars (or vars\_files, just below) for variables that must be used as entered.

See [Variable Precedence](using-variables.md)

### vars\_files

This works like vars but loads variables from YAML dictionaries according to the specified paths.&#x20;

The paths are relative to the playbook root for relative paths. The files should define YAML dictionaries (also known as hash tables or maps).  Any errors in YAML syntax will be noted during playbook evaluation.

###



###

###

###

###







