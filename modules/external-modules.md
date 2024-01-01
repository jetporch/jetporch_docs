# 📦 External Modules



{% hint style="info" %}
**Early Access**

This feature is currently available on the git source branches in with the following minor limitations.  All documented features are otherwise stable:

(1) failed\_when conditions should be on top of the return code behavior, not in place of them

(2) the module JSON conventions does not yet support the module determining it's own changed status by returning a 'changed' element in the JSON
{% endhint %}

While Jet's stock modules are all written in Rust, Jet also allows you to write modules using any programming language you want - whether that's Go, Python, Ruby, Intercal, or bash, it doesn't matter.

This system works by pushing out your external module/program to the target hosts (if in SSH mode, otherwise it's just copying it to a temp directory) along with a JSON file containing the host-specific arguments to that module. The module is then run by feeding the arguments as standard input into your module. After the module is run - regardless of success or failure status - Jet will remove both input files.

### Writing External Modules

[Here is a python example of a minimal module](https://git.sr.ht/\~mpdehaan/jetporch/tree/main/item/examples/playbooks/modules/demo\_external.py) - the module can be very short and there is no real requirement of using any particular libraries. If you are writing a module in bash you probably would want to use something like 'jq' to process the input. Examples for different programming languages will likely be added in the future.

```python
#!/usr/bin/env python3

import json
import sys
import fileinput

data = json.loads("\n".join([ x for x in fileinput.input() ]))
result = {}
data_a = int(data["a"])
data_b = int(data["b"])

result['sum'] = data_a + data_b
result['difference'] = data_a - data_b

msg = json.dumps(result)

print(msg)
sys.exit(0)
```



### Module Programming Conventions

If any variables are used in module inputs, the variables will come into the JSON as strings, so the external module code needs to cast accordingly.

Modules should take care to not output non-JSON text at least in non-failure conditions, as this will prevent their output from being parsed.&#x20;

If a module wishes to return a failure, it can also return non-0 exit status and include a human-readable value in a 'msg' JSON map attribute or just print traceback text. In crash / failure conditions, it's ok to return errors as they are (not as JSON), as non-JSON output will automatically be registered as a failure.&#x20;

### Calling External Modules

Modules are invoked in Jet playbooks with an '!external' module tag, like so:

<pre><code><strong>tasks:
</strong>    - !external
      use: your_module_name
      params:
          asdf: 1234
          jkl: "foo"
</code></pre>

{% hint style="info" %}
**That's Meta**

If you've been paying attention the above syntax, you might correctly guess that "External Modules" are implemented with a regular jet Rust module.  That's why "!external" is  used on every call, the name of the Jet Rust module that supports this feature is called "external".
{% endhint %}

### Module Search Paths

Modules will be looked for in a directory along the playbook called "modules/" by default.

Additionally, the CLI flag --modules (-m) can add additional search paths, as can the environment variable JET\_MODULES\_PATH:

```
./target/release/jetp local --modules ~/foo --playbook pb.yml
```

```
JET_MODULES_PATH=~/foo jetp local --playbook pb.yml
```

### Additional Features

Just like the command module, results of the module can be saved to a variable, and there are arguments available for adding to what the changed and failure convention of the module would be.&#x20;



[Here is a more complete example of the module being used](https://git.sr.ht/\~mpdehaan/jetporch/tree/main/item/examples/playbooks/external.yml):

```

- name: demo external module feature
  groups: 
    - all
  
  tasks:

    - !external  
      use: demo_external.py
      params:
         a: 1
         b: 2
      save: result
      failed_when: (lt sum 50)
      changed_when: false

    - !debug
      vars: 
        - result


```

Other than "use" and "params" the remaining parameters work like those in the "[shell](commands.md#shell)" module.

| Parameter     | Description                                                                                                                                                                                                         |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| use           | The name of the external module to use, including the file extension (if any)                                                                                                                                       |
| params        | Arbitrary parameters to the module.  This must be a JSON map/dict but can be arbitrarily nested at any level of depth.  Keep in mind in playbooks it is usually clearer to limit nesting and to keep things simple. |
| save          | Saves the result data in a variable.  All JSON data is included, in addition to a variable "rc" which includes the return code from the external module.                                                            |
| failed\_when  | If a supplied condition here is true, the module will record a failed status even if the module returned a zero exit code and valid JSON.                                                                           |
| changed\_when | A condition that controls when a change status is recorded for the module.  If not supplied, a changed status is always recorded                                                                                    |

