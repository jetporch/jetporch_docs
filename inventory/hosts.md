# 💻 Hosts



<img src="../.gitbook/assets/file.excalidraw (13).svg" alt="" class="gitbook-drawing">

A Host defines an IP address or hostname that jet can talk to, give orders, or configure.&#x20;

[Playbooks](../playbooks/playbook-overview.md) in Jet cannot work by naming specific hosts.  Playbooks address multiple hosts at the same time by referencing [Groups](groups.md). Groups contain the hosts.

Hosts _can_ be given specific [variables](../playbooks/using-variables.md) that override variables specified on the groups of which they are a member, but this doesn't mean they should.&#x20;

Generally, you should resist defining too many variables on hosts.  It's better to think in terms of groups as much as possible.

Occasionally a host may run on a non-default SSH port, or need to have an address defined that is different from the name of the host.  This, along with the username to login as, can be set by variables that are described on the [SSH](../connectivity/ssh-mode.md) chapter.



