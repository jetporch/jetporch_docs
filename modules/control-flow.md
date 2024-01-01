# 🔀 Control Flow

### Control Flow Modules Index

Control flow commands don't _manage_ remote systems, but they can share information with the user and alter the execution of the playbook.

These are somewhat similar to many of the [logic keywords in tasks](../playbooks/tasks-and-task-modifiers.md), and are frequently used in conjunction with those keywords.



| Module                           | Description                                                               |
| -------------------------------- | ------------------------------------------------------------------------- |
| [assert](control-flow.md#assert) | testing and safeguards                                                    |
| [debug](control-flow.md#debug)   | help writing playbooks                                                    |
| [echo](control-flow.md#echo)     | help writing playbooks and explaining steps in playbook output            |
| [fail](control-flow.md#fail)     | adding safeguards to playbooks or early stops when debugging              |
| [facts](control-flow.md#facts)   | gathering info about remote systems for use in conditionals and templates |
| [set](control-flow.md#set)       | combining variables and conditionally setting variables                   |

### assert

The assert module can fail a play for a given host if a certain expression criteria applies.

The assert module, as shown below, works on the same conditional syntax described with _cond_ on the [task page](../playbooks/tasks-and-task-modifiers.md), so we do not need to explain the handlebars expression syntax again here.

[An example is available in our examples repo for you to play with](https://github.com/jetporch/jetporch\_examples/blob/main/playbooks/learning\_assert.yml)

```
tasks:

   - !assert
     name: the OS must be Linux
     true: (eq jet_os_type "Linux")

   - !assert
     name: the OS must not be MacOS
     false: (eq jet_os_type "MacOS")

   - !assert
     name: various things must all be true
     all_true:
       - (eq dog "scooby")
       - (eq ghost "blinky")

   - !assert
     name: none of these things may be true
     all_false:
       - (eq jet_os_type "Atari")
       - (eq ghost "Slimer")

   - !assert
     name: one of these things must be true
     msg: something is wrong, the value of ghost is {{ ghost }}
     some_true:
       - 
```

{% hint style="info" %}
**Why Does 'msg' exist when there is 'name'?**

While template expressions cannot appear in 'name' - this is a banner that appears above all tasks and variables can be different per host - variables can appear in 'msg', which may be helpful in displaying the value of some variable without a seperate _echo_ task.
{% endhint %}



| Parameter      | Details                                                   |
| -------------- | --------------------------------------------------------- |
| **msg**        | Display this string when the assert fails                 |
| **true**       | A single handlebars expression that must be true          |
| **false**      | A single handlebars expression that must be true          |
| **all\_true**  | Multiple handlebars expressions that must all be true     |
| **all\_false** | Multiple handlebars expressions that must all be false    |
| **some\_true** | Multiple handlebars expressions of which one must be true |

### debug

The debug module shows all variables available to a host in templating, in the context of each host. This can be quite a lot of output, so the optional variables parameter can pare down the output to show just certain variables:

```
tasks:

   - !debug
   
   # OR ...
   
   - !debug
     vars:
        - foo
        - bar
```



| Parameters    | Description                                                                          |
| ------------- | ------------------------------------------------------------------------------------ |
| **variables** | An optional list of variable names to print, otherwise all variables will be printed |

### echo

Displays messages in the middle of a play.  This command does not involve any communication with the managed host and can be used to debug variable values in a basic way.  A more fully featured debug module will be available later.

```
tasks:
    - !echo
       msg: "For those about to rock, we salute you!"

    - !echo:
       msg: "The value of X is {{ x }}"
```



| Parameters | Description                               |
| ---------- | ----------------------------------------- |
| msg        | **Required**. String to show to the user. |

### fail

Fail unconditionally fails the rest of the play, which will result in all of the tasks in the play failing for every host, which will in turn result in the entire playbook execution being stopped.

This is not really a useful construct to leave in the automation, but it may be useful content when debugging some automation and wanting the automation to stop before it gets any further along in the playbook content.

{% hint style="info" %}
**Failing With Flexibility**

If you want to fail a playbook run for a host conditionally - when certain situations are true but not always - use the _assert_ module instead&#x20;
{% endhint %}

```
tasks:
   
   - !echo
     msg: "hello"
   
   - !fail
     msg: "this message is optional"
   
   - !echo
     msg: "things will never get this far"
   
```



| Parameter | Description                           |
| --------- | ------------------------------------- |
| **msg**   | Required. String to show to the user. |

### facts

The fact module reaches out to the remote system, determines various properties about the OS and state of that system, and inserts variables into the 'host' namespace for use in templates, conditionals, and so on.

{% hint style="info" %}
**When Are Facts Gathered And When Are Fact Variables In Scope?**

Fact gathering in Jet is never done implicitly. If you do not have a "!facts" task in a play, facts will not be gathered. This is because lots of playbook uses do not need them.

If the playbook has multiple plays, variables will persist between plays that involve the same hosts. If you would like to use the _facts_ module, you can put it at any point in the tasks list, but at the top is a good idea. It's fine to re-run _facts_ later to re-gather facts as well, should you think something has changed!
{% endhint %}

```
tasks:

    - !facts
      
    - !echo "the Linux OS type is {{ jet_os_flavor }}"
```

The core set of variables supported by Jet is intentionally somewhat small.  Values include:

| Target OS | Variables            |                                                                                                                                                                                                   |
| --------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Mac       | jet\_os\_type        | MacOS                                                                                                                                                                                             |
| Mac       | jet\_os\_flavor      | OSX                                                                                                                                                                                               |
| Linux     | jet\_os\_type        | Linux                                                                                                                                                                                             |
| Linux     | jet\_os\_flavor      | EL, Debian, Arch, etc                                                                                                                                                                             |
| Linux     | jet\_os\_release\_\* | variables from /etc/os-release namespaced and lowercased, such as:  jet\_os\_release\_is\_like, jet\_os\_release\_name, etc, cat this file on your distribution for understanding keys and values |
| All       | jet\_arch            | The architecture, such as x86\_64, as provided by 'uname'                                                                                                                                         |



Again, Jet's core set of facts is deliberately minimal.

To extend the levels of facts provided, Jet can use installed tools like facter and ohai (and soon, osquery) on remote hosts, by supplying parameters to the fact module:



| Parameter | Description                                                                                                                                                                                           |
| --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| facter    | **Boolean**.  If true, runs 'facter' on the remote host, saving facts in a variable called 'facter'.  Facter is a product of Puppet Labs. Facter comes from the configuration management tool Puppet. |
| ohai      | **Boolean**. If true, runs 'ohai' on the remote hosts, saving facts in a variable called 'ohai'. Ohai comes from the configuration management tool Chef.                                              |

Here is an example of facter being used:

```
 tasks:

     - !facts
       facter: true

     - !debug
       # so many facts!!!

     - !echo
       msg: "uptime is {{ facter.system_uptime.days }} days"

```



{% hint style="info" %}
**Need More Facts?**

More fact options will be added in Tech Preview 2, most likely including support for a /etc/jet/facts.d directory on the remote host.&#x20;
{% endhint %}

### set

set allows variables to be set conditionally or as the result of combination of other variables, something that is not generally allowed elsewhere in the program. Use this sparingly as it may impede playbook readability and obviousness.  This could be be used in roles that want to target multiple operating systems but the packaging between them is different in terms of file locations or desired default settings.

A conditonal set example:

```
- name: set example

  groups:
    - all
    
  defaults:
     debian_basedir: "/etc/foo"
     conf_location: "/etc/foo.conf"
    
  tasks:
  
   - !facts  
   
   - !set
     vars: 
        conf_location: "{{ debian_basedir }}/foo.conf"
     with: 
        condition: (eq jet_os_flavor "Debian") 
             
```

{% hint style="info" %}
**Need More Flexibility?**

Depending on demand a more structured case statement may also be available in the future, but we want to avoid having branching constructs in the automation language beyond skipping tasks.    &#x20;
{% endhint %}

{% hint style="warning" %}
**Pay Attention To Vars Vs Defaults**

If the word "defaults" was changed to "vars" above, the location would not be overridden correctly because "vars" has a much [higher precedence](../playbooks/using-variables.md) than host variables.
{% endhint %}

| Parameter | Description                                              |
| --------- | -------------------------------------------------------- |
| **vars**  | A mapping of string variable names to variables to set.  |

