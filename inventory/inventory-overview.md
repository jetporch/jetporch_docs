# 🔎 Inventory Overview

{% hint style="info" %}
**Understanding When Inventory Applies**

If wanting to configure machines in Jet over [SSH](../connectivity/ssh-mode.md), there are two basic key concepts to understand -- _Inventory_ and [Playbooks](../playbooks/playbook-overview.md).

If wanting to use Jet [locally](../connectivity/local-mode.md) or in the future local [agent](../connectivity/agent-mode.md) modes, you only need to understand [playbooks](../playbooks/playbook-overview.md). In this section, we will assume you _do_ want to deploy with SSH. If you want to learn Jet playbooks _locally_ first, you can skip this section and come back later.

To review how to select different deployment modes using _jetp_ subcommands, see [Executing A Playbook](../basics/command-line-usage.md).
{% endhint %}

**Inventory** tells jet (in SSH mode) which machines (arranged into [Groups](groups.md)) we want to control or talk to.

There are two ways of defining inventory:

1. [**File-based inventory**](file-based-inventory.md) organizes lists of hosts into groups using YAML files on the filesystem. This structure is really best understood by having GitHub open in a new tab, so open up the link in [example repo](../basics/example-content.md) for examples when reading the chapter.  File-based inventory is great for getting started and also for small development projects, though many users with production cloud configurations may not use it long term.
2. [**Dynamic inventory**](dynamic-cloud-inventory.md) is commonly used with cloud systems and "external sources of truth". With these, jet calls an executable script (or scripts) that return JSON. These scripts ask the external source (such as a cloud provider) what machines there are to manage, so no inventory information has to be described manually -- this is important because in cloud cases, in particular, inventory is always changing.

No matter how we choose to load it, Inventory is defined by two types of objects in Jet -- [Groups](groups.md) and [Hosts](hosts.md), which are explained further in the upcoming sections.

## Inventory Display

Inventory groups and hosts, as well as variables loaded from them, can be explored with the jetp CLI:

```

$ jetp show-inventory # shows the all group

$ jetp show-inventory --show-groups webservers

$ jetp show-inventory --show-hosts www01.example.com
```

