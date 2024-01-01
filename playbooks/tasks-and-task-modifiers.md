# ✅ Tasks & Task Modifiers

## Basics

Tasks are line items in [Plays](plays.md) or [Roles](roles.md) that make real things happen - they are the only thing in Jet that really do.  Each task is a parameterized call into Jet [Modules](broken-reference) - you should probably skim the list of them to understand what modules are! This will also make much more sense with our [examples](../basics/example-content.md) open in GitHub!

A task entry in a list of tasks looks like this:

```
- !module_name_goes_here
  name: optional comment about what the task does
  x: "value"
  y: 42
```

The key things to identify are the _name_ comment, the _module_ line, and the _parameters_.  Every task must have the _!module\_name\_goes\_here_ line (this is called a YAML _tag_), and all will have parameters - some parameters are required, some may be optional. The tag is not optional.



<img src="../.gitbook/assets/file.excalidraw (1) (1).svg" alt="" class="gitbook-drawing">

Exploring [Modules](../modules/module-overview.md) will give you the best idea of what you can do with tasks.  Beyond the module parameters, tasks can be further modified by the optional statements _with_ and, per se, _and, w_hich are shown below.

| Common Parameter | Description                                                               |
| ---------------- | ------------------------------------------------------------------------- |
| **name**         | Optional comment parameter that will be shown when the task executes.     |
| **with**         | Structure. Details special behaviors that happen before the task executes |
| **and**          | Structure.  Details special behaviors that happen after the task executes |

## Adding Logical Modifiers

Many tasks will be fine in a playbook just invoking a module, but sometimes additional behaivor is required.

Keywords explained below - "with" and "and" futher modify tasks.   Learn these _after_ exploring the [module documentation](broken-reference).

<img src="../.gitbook/assets/file.excalidraw (10).svg" alt="" class="gitbook-drawing">

## With Statements

With statements are one of two types of task modifiers.  With statements behaviors that happen _before_ a module executes or otherwise _alter_ the execution of a module.



| With Parameter   | Description                                                                                                    |
| ---------------- | -------------------------------------------------------------------------------------------------------------- |
| **condition**    | Only execute the task when this condition is true                                                              |
| **delegate\_to** | Run this task on a different host using the hostname as a reference                                            |
| **items**        | Loop over the task running it multiple times using items from a list                                           |
| **subscribe**    | For [handler](handlers.md) tasks only, run this task only when this event name is signalled by a _**notify**_. |
| **sudo**         | Change to a different user account when running this task.  This can use tools other than sudo.                |
| **tags**         | Apply labels that can be used with command line flags for selective execution                                  |

### condition

_condition_ runs a task only if certain conditions are true.

```
tasks:
 
  - !shell
    cmd: some_command --blarg
    with:
        condition: (gte xyz 42)
```

Condition can currently take the following boolean expressions.

* `eq`
* `ne`
* `gt`
* `gte`
* `lt`
* `lte`
* `and`
* `or`
* `not`

The parentheses around the condition are required.

More operators and shortcuts may be added in the near future.



{% hint style="info" %}
**Avoiding Undefined Variables**

Be careful when using condition with features like 'save' from the 'shell' module, because you can set variables that are defined for some hosts and then not defined for other hosts.  Later on in the playbook, this can lead to template evaluation errors.

You can use the '[set](../modules/control-flow.md#set)' module or the '[default](plays.md#defaults)s' keyword of a playbook to make sure all referenced variables always have a value.  In the near future the 'group\_by' module will provide ways of creating temporary groups where certain conditions are true, also preventing the need to attach a condition to so many tasks. &#x20;

Where possible, try to not "program in YAML" -- grouping machines appropriately in inventory and not using plays targetting the "all" group can usually help you not have to have many (or any!) conditions in a playbook.
{% endhint %}

### delegate\_to

Normally in SSH mode Jet is looping over a number of hosts and performing actions all on the indicated host.  The delegate\_to keyword implies a step runs on a _different_ host in inventory, or localhost, but all the variables and information about the remote host are available to the delegated host.

There is no concern that delegation from a large fleet of machines can overwhelm a host, but it can create a "slow" portion of a playbook when a large number of hosts are involved. Connections are guarded by per-host mutexes, so only one task can be run against the delegated target machine at a time.&#x20;

The variable "delegate\_host" is created automatically when using delegate\_to and refers to the current host being looped over in inventory.

{% hint style="danger" %}
**Why Localhost Delegation Requires A CLI Flag**

Delegation specifically to localhost has _potential_ security consequences because variables derived from remote facts may be too untrustworthy (for instance, the remote host might be compromised), especially when those variables are used to build module arguments.  As we risk to avoid information leakage and other suprises, delegating to localhost requires the --allow-localhost-delegation CLI parameter to acknowledge that these potential consequences are accepted and _any_ potential values of variables are acceptable to be used against localhost for any delegated task. If not provided, delegation is still allowed to other hosts. Jet will still check parameters for unsafe shell characters and so forth.

Localhost does not have to be in  inventory to delegate tasks to localhost.

Other hostnames do not require an opt-in flag to use delegate\_to.
{% endhint %}

```
- name: a playbook demonstrating delegation

  groups:
     - webservers
     
  defaults:
     monitoring_server: monitor.example.com
     
  tasks:
     - !shell
       cmd: /usr/bin/monitoring_add_host --host={{ delegate_host }}
       with:
           delegate_to: "{{ monitoring_server }}"
          
```

### items

items allows for tasks to be executed in a loop, preventing tedious data entry, and works with either variables or explicit lists:

```
- name: with_items demo

  defaults:
     apt_packages:
        - cowsay
        - tree
        
  tasks:
  
     - !apt
       package: "{{ item }}"
       with:
          items: apt_packages
          
     # OR ...
     
     - !apt
       package: "{{ item }}"
       with:
          items:
             - cowsay
             - tree
              
          
```

The variable "item" is provided to for each task and contains the value of each item that is looped over.&#x20;

items also works lists of maps (also known as hash tables or dictionaries), like so:

```
- name: with_items demo

  defaults:
     apt_packages:
        - { name: uno,  version: "1.23" }
        - { name: dos,  version: "3.45" }
        - { name: tres, version: "4.6" }

  tasks:
     - !apt
       package: "{{ item.name }}"
       version: "{{ item.version }}"
       with:
          items: apt_packages
                        
```

Note that the list of dictionaries must currently be defined in a variable of some kind in this case, they can not be defined in line inside "items".

There is a more generic example [in the example repo](https://github.com/jetporch/jetporch\_examples/blob/main/playbooks/items.yml) also showing some template evaluation features used with the items loop.

### subscribe

Subscribe is used only in [handler](handlers.md) sections.  It identifies the name of an event that will trigger the handler to conditionally run and must be matched exactly.  For clarity in playbook auditing, variable names are not evaluated within subscribe, but they are within _notify_.

```
handlers:
 
   - !sd_service
     service: redis
     restart: true
     with:
         subscribe: restart redis
 
```

{% hint style="info" %}
Omitting a _subscribe_ within a handlers section is a detectable error (it is required), just as is including a _subscribe_ within a non-handlers section is disallowed. In other words, get used to putting 'subscribe' on your handlers, or jetp will tell you about it!
{% endhint %}

### sudo

Sudo elevates the login user to a new user before running a task.  The actual command used need not be sudo. For support of other sudo-replacements, see the documentation in [Plays](plays.md).

```
tasks:
   - !shell
     cmd: /usr/bin/foo
     with:
         sudo: root
```

Sudo can also be set at play level as described in [Plays](plays.md), in which case all steps in the play are executed by sudo without need to set sudo to each task.  This value can also be set on the [command line](../basics/command-line-usage.md).

### tags

Tags are attributes that can be added to a task that normally don't do anything until the command line flag "_--tags_" is used.  When _--tags_ is provided, only the tasks with tags that match the command line are run.  This can be used to execute part of a playbook.  Tags can also be applied to roles directly, which results in much easier to read playbooks than tagging every single task.

```
tasks:
   - !shell
     cmd: /usr/bin/foo
     with:
        tags: ['foo']
```

```
$ jetp ssh --playbook pb1.yml --inventory ~/private_inventory --tags foo
```



## And Statements

"And" statements modify a task by adding more behavior that occurs _after_ a task is run.  It is fine to  use "and" and "with" statements together.  '_And_' keywords are not allowed in '_with_' sections and vice versa.



| And Parameter      | Description                                                                                                         |
| ------------------ | ------------------------------------------------------------------------------------------------------------------- |
| **ignore\_errors** | No matter what happens, don't count this module step as a failure, and continue to execute more tasks on this host. |
| **notify**         | Signal an event handler so that the named handler will run in a handlers section                                    |
| **retry**          | Integer. Attempt this task multiple times until it succeeds                                                         |
| **delay**          | Integer. Wait this many seconds between retries.                                                                    |

### ignore\_errors

ignore errors will ignore the failure results from a task despite most anything going wrong. It is a _very_ blunt instrument to use when you don't care if a task really succeeds or not, but you just want it to _try_. The _shell_ module for example contains much more fine grained error status controls, and that is the better way to deal with return codes for that module.

```
tasks:

  - !fail
    name: this will not fail
    and:
        ignore_errors: true
      
```

### notify

modules return a "changed" status internally when they perform changes on remote hosts.  This can be used to run certain tasks conditionally at the end of the play, only when required. &#x20;

This system is described in greater detail in [Handlers](tasks-and-task-modifiers.md#handlers). &#x20;

```
tasks:

   - !template 
     src: templates/foo.conf
     dest: /etc/foo/foo.conf
     and:
         notify: restart foo
        
```

### retry (and delay)

If a task fails, Jet can re-attempt the task for a given finite number of times with a certain number of seconds delay between the retries.

The default value for retry is 0 (no retries) and the default value of delay to wait before attempting another retry is 1 second. &#x20;

This would be most commonly used with 'netstat' when services are not immediately usable after they return from a successful start/restart or when waiting for a local or remote port to open.

```
  # wait for a file to exist
  
  - !shell
    cmd: cat '{{ test_file }}'
    and:
       retry: 20
       delay: 10
```

## See Also

Some users may expect to see some task keywords features that are not listed here.&#x20;

Saving variables from shell command results, or ignoring errors conditionally, are features directly embedded into the shell module (and possibly some other future modules), and are not generic task keywords. This was a language decision made to increase playbook clarity and readability. [See the shell module documentation for details](../modules/commands.md).

If there is another language feature you would like to see, [stop by Discord](../community/discord-chat.md) and let's discuss it!









