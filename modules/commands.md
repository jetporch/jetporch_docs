# ❗ Commands

Command modules execute programs or run scripts where there is no declarative module available.  Imperative (aka "non-declarative") actions in Jet are _not_ even remotely discouraged, and various parameters and task keywords can be used to make them behave more like declarative resource models as is shown below.

### Command Module Index

| Module                       | Description                                        |
| ---------------------------- | -------------------------------------------------- |
| [script](commands.md#script) | Push and run scripts and dynamic language programs |
| [shell](commands.md#shell)   | Run individual command line commands               |

Command modules run commands or scripts on the indicated managed system.  These are particularly useful when combined with [task keywords](../playbooks/tasks-and-task-modifiers.md).

### script

runs the contents of a script over the shell.&#x20;

{% hint style="warning" %}
**Coming Soon**

This feature will be included with the "Tech Preview 2" release.  A quick workaround is to use the _**copy**_ module and then execute it with the _**shell**_ module.
{% endhint %}

### shell

runs a shell command on a machine.

```
tasks:
    - !shell 
      cmd: "/usr/bin/foo --some-arguments 1234"
```

While this is really one of the simplest modules, there is some nuance to dealing with it as it pertains to variable usage and error handling considerations.&#x20;

<details>

<summary>Understanding Variable Safety</summary>

To _completely_ maximize safety, variables should not be put into the "cmd" argument line, however they can be used in safe ways, especially when you are sure variables came from defintiions in the git repository and not from remotely derived values, such as command output from a remote host. When using variables in the cmd parameter, care must be taken to trust _all_ variables in the 'cmd' argument and know where they come from. Variables should also always be quoted, though the program does not enforce this.

Why not?  Well, it's good for accidental errors but it's not a safety feature. Shell quoting of strings containing variables is unsufficient by itself to prevent shell escapes, such as inserting a semicolon and running a stealth "bonus" command. Thankfully, Jet looks out for these potential security problems. Nearly all parameters in Jet on input are already filtered for invalid shell characters.

By default, if the 'cmd' line contains variables, Jet will also _not_ permit potentially unsafe shell characters to appear in the resultant command input if they appear after the variables are evaluated. This will cause a failure in the playbook for each host where this occurs and the command will not be executed. Therefore the **unsafe** keyword is required to permit those shell characters to appear in situations where unsafe characters appear with variables, whether or not they appear in the variable expressions themselves or between them.&#x20;

This is a vital security consideration as information coming back from a remote host and sometimes a cloud environment (such as an instance tag variables set by another user) may be untrustworthy.&#x20;

**Unsafe** and remotely-sourced variables should never be used together with the "delegate\_to" keyword, because it would allow a compromised host to run shell commands of it's choosing on the control machine.

Here is an example of using the unsafe keyword:

```
   - !shell
      name: the unsafe command is very dangerous! read the warning below
      cmd: "/usr/bin/foo --param='{{ somevar }}' > '{{ somepath }}'.log"
      unsafe: true
```

When using unsafe, the variables must still be trusted by the user, this keyword only admits that the user is signing off that they are not afraid of the variables being used for shell injections.

</details>

<details>

<summary>Controlling When Failures Are Returned</summary>

Often a command may return a non-zero error code in a scenario that does not indicate a failure condition. Non-zero commands normally fail the task in the Jet play and keep it from continuing. A good example of this is grep which returns 0 on matching some text and 1 on not finding a match and we may wish to ignore this error.&#x20;

In these scenarios, _**failed\_when**_ can be used on the task. &#x20;

[The following example is available on GitHub](https://github.com/jetporch/jetporch\_examples/blob/main/playbooks/shell\_tricks.yml) and can be used as the basis for other experiments -- this puts together conditionals, variable evaluation, and various shell module parameters:

```
tasks:

  - !shell
    cmd: grep -i "chickens" /etc/motd
    failed_when: (gt rc 1)
    changed_when: false
    save: grep_result
     
  - !echo
    msg: "grep result was {{ grep_result.rc }}"   
     
  - !echo
    msg: "the motd mentioned chickens"
    with:
        condition: (eq grep_result.rc 0)
        
  - !echo
    msg: "the motd did not mention chickens"
    with:
        condition: (eq grep_result.rc 1)
        
```

The **failed\_when** parameter expression has access to all variables in the host that normal variable templating would have and is a handlebars expression as described in the documentation for [condition](../playbooks/tasks-and-task-modifiers.md).

In addition to all additional variables, **failed\_when** is given two more variables which are the ones most users will want to use - **rc**, the return code of the command, and **out**, the full text output of the command, merging standard out and standard error into one string.&#x20;

Without **save**, also shown, the variables do not persist for access in subsequent tasks. Save requires a variable name to store the values in.

**failed\_when** can also be used to completely disable return code checking, much more simply, like so:

```
tasks:
    - !shell
      name: completely ignore the error code
      cmd: /usr/bin/foo
      failed_when: false
```



</details>

<details>

<summary>Controlling When Changes Are Reported</summary>

Finally, many users like the the idea of a playbook reporting that no changes needed to be made on remote hosts as a sign that hosts match desired policy. Since the grep command does _not_ make changes on any remote hosts, we can mark it as such with **changed\_when**.  This way, the playbook summary at the end of the playbook run will give better visibility of what changes Jet actually made on the host.

```
 - !shell
   cmd: grep -i "chickens" /etc/motd
   failed_when: (gt rc 1)
   changed_when: false
   save: grep_result
```

Should we wish to mark another command as conditionally changing things based on return code, failed\_when uses the same conditional syntax used above and also described in detail with the '[condition](../playbooks/tasks-and-task-modifiers.md)' task keyword.

```
 - !shell
   cmd: /usr/bin/arbitrary_cmd --example-only
   failed_when: (gt rc 1)
   changed_when: (eq rc 1)
   save: grep_result
```



</details>

| Parameter         | Details                                                                                                                                                 |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **cmd**           | The command to run                                                                                                                                      |
| **failed\_when**  | Handlebars expression using 'rc' and 'out' to decide whether the command is a failure.  If not provided, errors are when the return code is non-zero    |
| **changed\_when** | Handlebars expression to decide whether the task effected a change on the remote host. If not provided, the system will assume yes.                     |
| **save**          | Stores the result of the command in a variable, \<variable>.rc and \<variable>.out are available permanently in the host's namespace until overwritten. |

