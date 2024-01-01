# 📂 File-Based Inventory

Inventory in Jet can be defined using YAML files on the filesystem.&#x20;

{% hint style="info" %}
**When To Use File Based Inventory**

While some large installations may adopt file-based inventory, many cloud users and larger installations may be interested in [dynamic inventory](dynamic-cloud-inventory.md) instead. Still, new users may find it easier to learn with this file-based inventory inventory, and it makes an excellent starting point. If this is your first time with Jet, we recommend trying file-based inventory at least once as you are getting going, before switching to dynamic inventory.
{% endhint %}

{% hint style="info" %}
**Have Experience With Similar Tooling?**

Users coming from Ansible will note that our group definition files have a different format than the INI-like or YAML-based inventory supported in Ansible. Forget what you know and read the documentation below. Our format has been designed to have less nesting and variance. _Ansible is a registered trademark of Red Hat Inc._
{% endhint %}

For understanding static inventory, it is easiest to show an example. [Please open GitHub](https://github.com/jetporch/example\_content/tree/main/inventory) in another browser tab as we go.

With examples open, let's break it down. Suppose we have a directory structure like this:

```
./inventory_folder/
    groups/
        group1                  # YAML files of a specific format
        group2
    group_vars/
        group1                  # arbitrary YAML maps for variables
        group2
    host_vars/
        hostname1.example.com   # arbitrary YAML maps for variables
        hostname2.exmaple.com
```

## Group Files

A group file looks like this:

```
# filename: groups/boston - do not add a .yml extension

subgroups:
  - boston-dc1
  - boston-dc2

hosts:
 - asdf.example.com
 - jkl.example.com
 
 
```

Basically it's just a simple list of directives:

### subgroups

Subgroup declares that the group, in this case, "boston', has a group of machines below it and that the group "boston" automatically includes all hosts referenced in those group files. &#x20;

This keyword is _optional_ and does not have to appear in the file.

### hosts

Declares a list of hosts are members of the named group. &#x20;

A groups file does not have to define any hosts if it only wants to define subgroups for a given group.  In other words, both '_subgroups_' and '_hosts_' are optional keywords in a group definition.

## Assigning Variables

What's above is all there is to group definitions.  There are additional steps to add variable information.&#x20;

The above commentary on directory structure showed a 'group\_vars/' and 'host\_vars/ directory.&#x20;

See [Inventory Variables On Disk](inventory-variables-on-disk.md) for how to assign values to inventory using that structure by adding these parallel folders. This structure can also be used with [dynamic/cloud inventory](dynamic-cloud-inventory.md) so it is very much worth learning. &#x20;

