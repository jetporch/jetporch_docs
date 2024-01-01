# 🎭 Roles

You should have already seen how roles are included in playbooks in the [Play](plays.md) documentation chapter.  This chapter describes how to define them, now that you have seen how to reference them.

To review, Roles are a way of organizing [tasks](tasks-and-task-modifiers.md), [variables](using-variables.md), handlers, and so on together to enable reuse across [Groups](../inventory/groups.md) and different [Plays](plays.md).&#x20;

We _highly_ recommend using Jet roles for non-trivial automation projects once you understand the basics of Jet.&#x20;





<img src="../.gitbook/assets/file.excalidraw (4).svg" alt="Role Directory Structure" class="gitbook-drawing">



{% hint style="info" %}
**More Role Powers Coming Soon**

Starting with Tech Preview 2, roles will also be able to package/embed [custom modules](../modules/external-modules.md) for easy distribution.&#x20;
{% endhint %}

[The role format is shown in our GitHub examples](https://github.com/jetporch/jetporch\_examples/tree/main/roles/redis) and will be much easier to understand roles with GitHub open in a different browser tab.

A role typically represents a function a given system should perform, such as "security\_policy" or "application\_server", but they can represent any kind of behavior, application, or policy.

Roles are found in search paths specified by _--roles-path_ (or enviornment variable _JET\_ROLE\_PATH_) as described in the [CLI Guide](../basics/command-line-usage.md) ... additionally, roles will be looked for in a "roles/" directory alongside playbooks.  Using the search path, it is possible to easily use roles in different git repositories.

The roles structure is shown below.&#x20;

```
rolename/

   role.yml

   ./tasks/*.yml
   ./handlers/*.yml
   
   ./templates/*.hb
   ./files/*.*
```



{% hint style="info" %}
**Role Parameters**

Roles can be parameterized as shown in the example in the [Plays](plays.md) documentation, in which case this acts as if setting additional _vars_ parameters that are only applicable to the scope of that role.  This is shown in the playbook examples.  There is not a list of what parameters can be allowed in a role invocation, they really can set any variable name they want.
{% endhint %}

### role.yml

Every role must have a role.yml file in the root of the role directory which can look like the following:

```
name: an optional comment

defaults:
   alpha_port: 2001
   beta_port: 2010
   gamma_port: 2061
   mogwai: "gizmo" 
   
tasks:
   - tasks1.yml
   - tasks2.yml
handlers:
   - handlers1.yml
   - handlers2.yml
```

All sections other than _name_ are optional, but there must be a _role.yml_ file to have a role.  The name must be exactly named _role.yml_ verbatim, and cannot be your-role-name.yml, or Jet will not find it.

{% hint style="info" %}
**No Include Statements**

There is intentionally no _include_ statement or task in Jet that can be used in the middle of some tasks block, it is the _role.yml_ file itself does the including.  This is chosen for clarity in auditing playbook content and to simplify the language.  Things must be explicit and decided up front.
{% endhint %}

A few pathing notes:

* Paths in '_tasks_' will be assumed to be relative to roles/$rolename/tasks/ unless they are absolute.
* Paths in '_handlers_' will be assumed to be relative to roles/$rolename/handlers/ unless they are absolute.

It is strongly encouraged to put these files in those locations.

### tasks/

The 'tasks' section refers to lists of [tasks](roles.md#tasks) that live in role\_name/tasks. &#x20;

The values in this file are a flat list of task objects like you might see under a 'tasks:' keyword in a playbook file, like so:

```
# example: tasks/tasks1.yml

- !shell
  name: a first task
  cmd: echo foo
  
- !template
  name: a second task, configuring foo!
  src: foo.conf.jb
  dest: /etc/foo/foo.conf
  attributes: { owner: michaeldehaan, group: michaeldehaan, mode: 0o600 }
  and:
      notify: restart foo
```

A few notes:

* If a task file isn't mentioned in role.yml it will not be included.  Other unreferenced files in this path will also be ignored.
* Note that the type of the tasks file is a  YAML list and there is no "tasks:" at the top of the file.
* This directory does not have to exist unless tasks are referenced in the role.yml.

### handlers/

Here the system looks for explicitly named files listed in the "handlers:" section of the role.yml file

The values in this file are also a list of [handler](roles.md#handlers) objects like you would see under the 'handlers:' keyword in a playbook file:

```
# example: handlers/handlers1.yml

- !sd_service
  service: foo
  restart: true
  with:
      subscribe: restart foo
      
```

The same details from tasks above apply here:

* If a handler file isn't mentioned in role.yml it will not be included. Other unreferenced files in this path will also be ignored.
* Note that the type of the handlers file is a  YAML list and there is no "tasks:" at the top of the file.
* A directory does not have to exist unless tasks are referenced in the role.yml.

{% hint style="info" %}
**Does A Handler Have To Contain Tasks?**

No. A handlers section can exist in a role without a role tasks section, but this could only serve to act on the notifications of other roles that executed previously in the current play. &#x20;

Similarly, it is true that event notifications in one role can be acted on in later roles in the same play.  Once a new play starts, all handler notification signals are cleared.

Either way, this would be a relatively uncommon way to use roles.
{% endhint %}

### files/

The copy module will look for files here when processing tasks in the active role, when trying to figure out what a "src" parameter points to.&#x20;

Outside of roles, this would normally for a folder named 'files/' next to the playbook directory.

This directory does not have to exist unless the role uses the copy module.

### templates/

The template module will look for files here when processing tasks in the active role, when trying to figure out what a "src" parameter points to. &#x20;

Outside of roles, this would normally look in a folder named 'templates/' next to the playbook directory.

This directory does not have to exist unless the role uses the template module.

###

###



