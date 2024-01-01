# 💻 Local

Local mode means the playbook just affects the local machine that is running jetp, and no remote connectivity is involved. It is the only Jet mode designed for configuring just a single machine.

<img src="../.gitbook/assets/file.excalidraw (8).svg" alt="" class="gitbook-drawing">

See our [CLI documentation](../basics/command-line-usage.md) for details.

In local mode, inventory is ignored.

Local mode can be used to build VM and container images from Jet Playbooks, in addition to setup of development environments.  It can also be used when executing jet from other management systems.



{% hint style="info" %}
**Check Mode Reminder**

As per the documentation on [Command Line Usage](../basics/command-line-usage.md), local mode can be run in two ways -- "local" mode which will configure the system, and "check-local" which returns what jet thinks would happen if running "jet local".  [SSH](ssh-mode.md) has this feature as well.
{% endhint %}

