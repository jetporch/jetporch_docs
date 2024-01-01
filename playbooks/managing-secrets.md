# 🔒 Managing Secrets

This chapter is about how to use secret values in production and avoid accidental leakage into logs. This works using Jet's ability to get variables from the environment.  Therefore, environment variable support in jet is not technically secret specific but is designed to work _mainly_ with secrets due to some _intentionally_ designed limitations that protect against secret leakage.&#x20;

Here is an example of passing environment variables into Jet:

```
$ FOO=1 BAR=2 jetp ssh --playbooks pb1.yml --inventory ~/path/to/inventory
```

Contrary to this basic example (which is good for learning), when dealing with secrets, we would _not_ normally get environment variables from the shell. The better way to inject variables into Jet is "--extra-vars" (-e) as described in [Command Line Usage](../basics/command-line-usage.md). As such, when we use variables like this, we are going to be intending them for secrets.

Naturally, we would not want to put secret values on the command line where they appear in shell history. Secret values would be loaded in from various secret tools like this example from 1Password's op run:

```
op run --env .env -- jetp ssh --playbooks pb1.yml --inventory ~/path/to/inventory --loadenv
```

In the above shell example, op run executes jetp in a subshell, passing extra secrets directly to jetp as unencrypted values.



<img src="../.gitbook/assets/file.excalidraw (10) (1).svg" alt="Where Secrets Flow" class="gitbook-drawing">

Environment variables (not just "secrets") will be made available to the the [template module](../modules/files.md) only, allowing our environment variable support to be quickly tested without using a particular secret tool:

```
# foo.conf.hb

[excellent_application]
foo={{ENV_foo}}
bar={{ENV_bar}}
splines=5
reticulate=1
```

Since only the template module is supported for this, we can not access secret values like so:

```
tasks: 
  - !shell
    cmd: "echo hey I shouldn't tell you this but bar is {{ ENV_bar }}
```

That will produce an error.  This is very intentional.

Only supporting the template module is done to make sure that values cannot be leaked into build logs or shell history accidentally.&#x20;

Redaction features from tools like "op run" provide an extra measure of log and output protection, which could be valuable if someone attempted to execute cat on a produced template or something of that nature to help with debugging.

This feature is not fool proof.&#x20;

It is designed to prevent accidental leakage of secrets to the wrong parties, but with access to source control or build scripts, things change quickly.&#x20;

Among those with sufficient access, mandatory code review prior to deployment is the best solution to possible insider threat issues, which is why Jet is designed around auditability and predictable execution.





&#x20;

