# 🔁 Using Variables

Variables are values that can be substituted in templates (via the template module) and logic throughout Jet's [playbook](playbook-overview.md) language. _Most_ strings in Jet are templated for evaluation, so variables can be used almost anywhere in the system.

Variables can be set in [inventory](broken-reference) -- in which case they can come from on disk or discovered from cloud parameters or in [playbooks](broken-reference).





<img src="../.gitbook/assets/file.excalidraw (1) (1) (1).svg" alt="" class="gitbook-drawing">





### Use In Tasks

\
Variables set elsewhere in the playbook can be directly used in playbook statements like so:





```
tasks:
   - !shell
     cmd: /usr/bin/example --argument1 {{ argval }} --argument2 asdf
```



The templates are processed by the Rust [handlebars](https://docs.rs/handlebars/latest/handlebars/) library, passing through all variables that are defined in the Jet system.



{% hint style="info" %}
**Where Variables Can Be Used**

Templating in Jet is very deliberate by design to encourage predictable and highly auditable content.

Only module _parameters_ in Jet are templated. For reasons of clarity, it is not possible to use variable expressions in the "_**Play**_" object (like the _**vars**_ keyword, or in paths to **vars\_files**) or nest the values of variables inside _other_ variables. In other words, variable evaluation is not recursive.

All of these decisions were made to make playbooks extremely predictable, avoiding many possibilities of hidden errors or surprises.&#x20;

Use variables when needed, but it is important to also realize that Jet is not meant to be a programming language, and the system is designed to enforce "best practices" in automation design by sometimes limiting the ability to do confusing things.  There may be times when you might want to generate a _**vars\_files**_ file for Jet from a script if you need data denormalized in many different ways.

Use of templates parameters in unsupported areas will produce largely obvious and up-front errors messages, such as a YAML boolean field not accepting a string value, allowing for easy correction.
{% endhint %}





### Use In Templates



The template module also uses Rust's handlebars library to describe the files it wants to write on the remote machines.  An example source template might use basic variables but it can also use other constructs.



```
# foo.config template example

[config section]
xyz = {{ xyz }}
backup_enabled = true

{{#if (eq abcd debian}}
tribbles = auto 
{{else}}
tribbles = 5000
{{/if}}

```



The Rust handlebars library provides the some compatible block expressions that are described in the Javascript handelbarjs documentation.  To keep things simple, these are the most commonly used and appropriate.

* `{{{{raw}}}} ... {{{{/raw}}}}`
* `{{#if ...}} ... {{else}} ... {{/if}}`&#x20;
* `{{#unless ...}} ... {{else}} .. {{/unless}}`
* `{{#each ...}} ... {{/each}}`
* `{{#with ...}} ... {{/with}}`&#x20;



As described with cond in [Tasks](tasks-and-task-modifiers.md), if statements can use the following boolean expressions:

* `eq`
* `ne`
* `gt`
* `gte`
* `lt`
* `lte`
* `and`
* `or`
* `not`



The '_if_' template example above shows the example of using if with _eq_, which must be surrounded in parentheses.  This is also shown in the examples for the 'cond' [task](tasks-and-task-modifiers.md) keyword.  The other expressions work the same way.

In addition to the built-in expressions, Jet adds the following expressions



<table><thead><tr><th>Expression</th><th>Description</th><th width="202">Version Added</th><th>Example</th></tr></thead><tbody><tr><td>contains</td><td>is a substring in a string?</td><td>0.2</td><td>{{ #if (contains x "foo") }}</td></tr><tr><td>ends_with</td><td>does a string variable end with a given string?</td><td>0.2</td><td>{{ #if (ends_with x "foo") }}</td></tr><tr><td>isdefined</td><td>is the given variable name defined?</td><td>0.2</td><td>{{ #if (isdefined foo) }}</td></tr><tr><td>to_lower_case</td><td>returns the string in lowercase</td><td>0.2</td><td>{{ to_lowercase foo }}</td></tr><tr><td>to_upper_case</td><td>returns the string in uppercase</td><td>0.2</td><td>{{ to_uppercase foo }}</td></tr><tr><td>starts_with</td><td>does the string variable start with the given string</td><td>0.2</td><td>{{ #if (starts_with x "foo") }}</td></tr><tr><td>trim</td><td>return the string without leading or trailing spaces</td><td>0.2</td><td>{{ trim foo }}</td></tr><tr><td>trim_start</td><td>return the string without leading spaces</td><td>0.2</td><td>{{ trim_start foo }}</td></tr><tr><td>trim_end</td><td>return the string without tailing spaces</td><td>0.2</td><td>{{ trim_end foo }}</td></tr></tbody></table>

With the exception of isdefined, these functions directly mirror their behavior in the Rust language.

A combined usage example showing some of these concepts put together:

```
{{#if (isdefined foo) }}
foo={{ to_lower_case foo }}
{{/if }}

{{#if (contains bar "elephants") }}
elephants=1
{{/if }}
```

{% hint style="warning" %}
**No Recursive Evaluation**

To encourage predictability and easily auditable content, templates in Jet are _**not**_ evaluated recursively, but only a single time. If you think you need variable indirection where a variable has another variable name in it, consider restructuring your data.  Where neccessary, variables such as paths that were stored this way...

&#x20;    base\_dir: /opt/apps

&#x20;    app\_path: "\{{ base\_dir \}}/foo/bar"

Can be recomposed like so:

&#x20;    base\_dir: "/opt/apps"

&#x20;    app\_sub\_dir: "/foo/bar"&#x20;

And then inside of the template file or module, just do this:

&#x20;   app\_path=\{{ base\_dir}/\{{ app\_sub\_dir \}}
{% endhint %}



Users familiar with Jinja2 will note that the variable dereferencing (double curly brackets) are the same but the other operations are not.  Handlebars is a deliberately more minimal system, and is not designed to be used in a 'programming' type capacity.  Over time we may provide some additional helper functions for templates, but will try to keep this very constrained to encourage predictable read-throughs of automation content.

### Advanced Topics 1: Variable Precedence

If learning Jet, you may not need to understand this immediately.  However, as you get further along, you will probably need to be aware of these details.



Variable precedence works like this:



<img src="../.gitbook/assets/file.excalidraw (5).svg" alt="" class="gitbook-drawing">

It is possible to define a variable in many locations throughout Jet.   When a variable is referenced, how do we decide what value is used? The variable source closer to the top of this list wins:

1. Variables used on the command line always win.
2. Playbook variables have the next highest priority.&#x20;
   1. vars\_files
   2. vars
3. Inventory variables have middle priority, and the is based on where the variable is in the inventory tree:
   1. Variables defined on a [Host](../inventory/hosts.md) system will beat out _Group_ variables. Host variables include variables stored as the result of a 'save' task action when running a module.
   2. Variables defined on a [Group](../inventory/groups.md) will be used if the same variable isn't set on the Host.  If there are subgroups that repeat variable names, the most specific group wins over the larger parent group.  For example values in "london" (a city group) would win over "england" (a country group).
4. Default variables have the lowest priority.&#x20;
   1. Variables defined in the 'default' keyword of a playbook
   2. Variables defined in the 'default' folder for a [Role](roles.md)



{% hint style="info" %}
**How can I avoid commiting this to memory?**

We hear you! - variable precedence is a neccessary evil because it exists and people will want to ask questions about it, but really, you're not meant to rely on it too much.  Defining variables in the right place makes things easier, so we might suggest:

Keep variables about _behavior and purpose_ inside of roles and role defaults.

Keep variables about _location and environment_ (stage vs prod, or customer vs customer) in inventory.  Understand precedence hierarchy with respect to inventory.

Use vars/vars\_files for things that are essentially constants that never change between location, environment, or type of system, and do not need to be repeated between playbooks, such as things to be used in simple on-off playbooks.

Use -e for things like release versions, but otherwise only sporadically.
{% endhint %}

### Advanced Topics 2: Variable Merging

Here's another topic you may not need to even use, but should be aware of if you try it.

If a _list-type_ variable is defined in multiple places, the contents of both lists will be added together.&#x20;

For instance, a default group variable could define a default list of base packages and an inventory subgroup variable would add more base packages to it rather than replacing the original list.

Similarly, if a hash/mapping/dictionary variable is defined in multiple places, the keys of the hash/mapping/dictionary will be merged together creating a larger hash. If any keys are used between the two variables, the one with greater precedence will overwrite the keys of the lesser.

