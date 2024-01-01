# 📂 Inventory Variables On Disk

Whether a [File-Based](file-based-inventory.md) or [Dynamic inventory](dynamic-cloud-inventory.md) is used, a "group\_vars/" and "host\_vars/" Variable folder can exist alongside an inventory source. This allows variables to be defined in YAML that are added to the inventory, regardless of where it came from.&#x20;

{% hint style="info" %}
**Reminder: When Does Inventory Apply?**

While mentioned earlier, it may help repeating that inventory in Jet is an _SSH-only_ concept and does not apply to local or future deployment modes. In fact, it is an error to specify --inventory when trying to execute a playbook locally.
{% endhint %}

For file-based inventory, Jet looks alongside the '_groups/_' folder to see if there are any files with the same names as the groups.  When it looks for files, it looks for files _without_ an extension:

```
# directory listing
# used by command like:
#    jetp [mode] --inventory path/to/inventory [...]

path/to/inventory/
    groups/          
       webservers
       boston
    group_vars/
       webservers
       boston  
    host_vars/
       webservers
       boston
```

When using dynamic inventory, Jet looks for the group\_vars/ and host\_vars/ directories parallel to the inventory scripts used. &#x20;

```
# directory listing
# used by command like:
#    jetp [mode] --inventory path/to/inventory/dynamic_inventory_script.py [...]

path/to/inventory/
    groups/          
       webservers
       boston
    group_vars/
       webservers
       boston  
    dynamic_inventory_script.py
    dynamic_inventory_script.ini  
    
```

File formats are the same regardless of the inventory type.&#x20;

Both '_group\_vars/_' and '_host\_vars/_' files are YAML hashes like so:

```
asdf: 1234
a_list:
   - one
   - two
   - three
a_nested_nash:
   dog: fido
   cat: fluffy
```

We strongly suggest keeping the inventory folder in source control, possibly as a seperate repository from the playbook content.

###



