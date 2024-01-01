# ☁ Dynamic/Cloud Inventory

In nearly all real-world production cases, managed computers, nodes, or systems (whatever we call them) dynamically come and go at all times, and what is currently present is rarely defined by files on disk or in version control. Cloud tools may create systems from CI/CD workflows, or systems may be controlled by autoscaling groups.&#x20;

In these cases, Jet's [on-disk inventory format](file-based-inventory.md) cannot be the "source of truth" for the list of groups, hostnames, and often even variable information.

{% hint style="info" %}
**Reminder About When Inventory Is Needed**

In local mode and in the future [agent](../connectivity/agent-mode.md) modes, inventory is **not required** and this section can be skipped. Inventory is an [SSH](../connectivity/ssh-mode.md)-only feature.
{% endhint %}

SSH Dynamic inventory in Jet works by calling one or more executable scripts or programs that return a specific JSON format to tell jet about groups, hosts, and variables.



<img src="../.gitbook/assets/file.excalidraw (9).svg" alt="" class="gitbook-drawing">



To use an inventory script, we simply invoke jet passing the executable file as a parameter:

```
$ chmod +x ~/some_script.py
$ jetp show-inventory --inventory ~/some-script.py --show-groups all
$ jetp ssh --inventory ~/some_script.py --playbook pb1.yml
```

These scripts could contact a  cloud provider, commercial CMDB, in-house database, or essentially any program. All that matters is they return the correct JSON data on standard output. Development of your own in-house inventory scripts (or forking our own) is encouraged, should available ones (detailed below) not work exactly how you like.

{% hint style="info" %}
**Adding Variables From Files**

Inventory variables from dynamic inventory can still be supplemented with variable information from the filesystem.&#x20;

If the inventory script has a "group\_vars/" or "host\_vars/" directory in the same directory as the inventory script and parallel to it, they will added to the groups and hosts returned by the inventory script.&#x20;

This is sometimes much easier and clearer than trying to assign variables through something like cloud tags, which some inventory scripts support.

See [Inventory Variables On Disk](inventory-variables-on-disk.md) for how this works.
{% endhint %}

## Where To Find Inventory Scripts

While you can write your own inventory scripts, you can find several for popular inventory sources in the ['contrib/inventory' directory of the main repo checkout](https://git.sr.ht/\~mpdehaan/jetporch/tree/main/item/contrib/inventory).

Because the inventory script specification is based on JSON, it is relatively easy to write inventory scripts in a wide variety of programming languages. If you write an inventory script for a popular computing platform, we would be interested in adding it to the collection.

For ease of collective maintenance, we would accept python and Rust in tree. Because these modules will usually be waiting on external APIs, there is _not_ much need for them to be implemented in Rust, but Rust's compiler checks are great to have and encourage more refactoring and keeping things up to date compared to python. Still, you may find python easier to write and that is also fine!

{% hint style="info" %}
**Legacy Code Still Present?**

This folder of inventory scripts was started as a fork of the Ansible inventory scripts from Ansible 2.9, the last Ansible release that contained stand-alone inventory script examples in tree before moving to an ansible-specific plugin architecture.&#x20;

Over time, any legacy references to "module\_utils" from the ansible codebase can be removed until it ends up being a self-contained set of scripts without any dependencies on "lib/jeti".  Most of "lib/jeti" deals with (unwanted) python 2.X support, which is not important to the Jetporch project, since we can assume a newer control machine and do not require python to be installed on remote systems.

Also, when looking at scripts in the project you may see many different parsing command line options. Jet does _not_ use these, and many of these were added for debugging purposes or to support a legacy Ansible parameter "--host" that Jet does not use. These can also be removed over time from Jeti. [See the readme in contrib/inventory](https://git.sr.ht/\~mpdehaan/jetporch/tree/main/item/contrib/inventory/README.md) for more information.

_Ansible is a registered trademark of Red Hat, Inc._
{% endhint %}

## Dynamic Inventory Specification

With some discussion of Jeti out of the way, let's describe the JSON format if you want to write your own inventory script instead of using one that has already been developed.

At the most basic level, the inventory script should return a JSON map (also called a "hash table" or "dictionary") with each top level key being the name of a Jet group. &#x20;

Each element can contain different elements, _all_ of which are optional. We'll start off with an example that just shows _children_ and _hosts_.

<pre><code><strong>{
</strong>    "webservers": {
        "children" : [   
            "webservers_raleigh",
            "webservers_newyork"
        ],
    },
    "webservers_raleigh" : {
        "hosts" : [
            "raleigh-www-01.example.com",
            "raleigh-www-02.example.com"
        ],
    },
    "webservers_newyork" : {
        "hosts" : [
            "newyork-www-01.example.com",
            "newyork-www-02.example.com"
        ]
    },
    "dbservers" : {
        "hosts" : [
             "db1.example.com",
             "db2.example.com"
        ]
    }
}
</code></pre>

In the above example, the JSON defines multiple groups - in this example, _webservers, webservers\_raleigh, webservers\_newyork_, and _dbservers_.

The valid subkeys "_children_" and "_hosts_" are both optional. For example, the group "webservers" has no hosts as direct members, but it does contain subgroups that have hosts.

{% hint style="info" %}
**More About Calling Conventions, Input, and Output**

How should an inventory script behave?  It should merely output JSON when called.

There is no need to have sorted keys or pretty-print the JSON, this is only shown for explaining the format. It might be helpful to pretty-print JSON if called with a particular flag, but Jet will not pass any flags to the script or program. Inventory programs/scripts in Jet do not pass any arguments or set any environment variables.

Any data loading from a configuration file, environment variable, and so on is up to that particular inventory script/program to decide. Typically they will load a file with the same base filename as the inventory script and a different extension.

If the program/script would like to return an error, it can return a non-zero exit code and any standard out or standard error messages from that failure will be shown to the user.
{% endhint %}

### Adding Group Variables

Variables can be added to any group by adding a "vars" keyword to the group definition.  The "vars'" must be a map/hashtable/dictionary as shown below, and can contain nested values, including hashes and arrays:

```
    "webservers_raleigh" : {
        "hosts" : [
            "raleigh-www-01.example.com",
            "raleigh-www-02.example.com"
        ],
        "vars" : {
            "foo"      : "bar",
            "baz_port" : "8080"
        }
    }
```



{% hint style="info" %}
**Must Groups Have Hosts In Them?**

Groups without hosts in them can still have variables assigned to them, just like groups can have hosts assigned to them without variables.  Having a completely empty group without subgroups is not very useful, but it will also not result in an error either.  The inventory script loading code in Jet will _not_ warn if a group with variables is never assigned hosts, because it is expected for the script code to construct inventory correctly.
{% endhint %}

### Adding Host Variables

Host specific variables can be added by adding a "hostvars" section to any group. &#x20;

```
    "webservers_raleigh" : {
        "hostvars" : {
            "raleigh-www-01.example.com" : {
                "asdf" : "example value"
            }
            "raleigh-www-02.example.com" : {
                "asdf" : "example value 2"
            }
        }
    }
```

{% hint style="info" %}
**Shortcuts**

When using "hostvars" in the script's per-group JSON, it is _not_ required to have a seperate "hosts" JSON element, though both will be processed if present.  _Hostvars_ could be considered an 'advanced' way to specify hosts.  This value can also be an empty dictionary "{}".
{% endhint %}

{% hint style="info" %}
**Magic Groups?**

To support compatibility with legacy ansible inventory scripts, host specific variables can also be added to an imaginary top-level group named "\__meta"._ If writing a new inventory script, don't do this.

Group names that start with a hyphen _will not be created as real addressable groups in Jet._ Jet interprets hyphen-meta as referring to what Jet calls the "all" group, though explicitly saying "all" instead of "\_meta" is preferred. &#x20;

Every group in Jet is implicitly added to the "all" group, there is no need to define an "all" group unless wanting to assign variables to that group, in which case they basically would serve as default variables, having a precedence level just a tiny amount higher than the defaults keyword in a playbook.
{% endhint %}

{% hint style="info" %}
**Magic Variables?**

Very few variables in Jet are considered magic and these are described in the [SSH chapter](../connectivity/ssh-mode.md).  They are all host-specific inventory variables.
{% endhint %}





