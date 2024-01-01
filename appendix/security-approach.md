# 🔐 Security Approach

Jet seeks clarity and auditability of automation content, correctness, and predictability in automation content first and then performance second.&#x20;

These design goals includes reducing opportunity for accidentally insecure practices with the tool, secrets disclosure, local disk exploits, cryptographic problems, and not over-enabling insider threat risk. This focus will always dominate over other design decisions.

### Readability And Insider Threat Mitigation

This is probably the most important thing we can do - we want _**predictability**_ vs _cleverness_.&#x20;

Language flexibility is important, but _**predictability**_ will always be chosen over flexibility.&#x20;

We want _anyone_ who does not know Jet but knows Linux/Unix administration to be able to read and mostly understand what a playbook would do.&#x20;

We also want it to be hard to obfuscate malicious configuration intent.  Further, Jet's internal code itself should be similarly readable. Features that can tend to turn automation into a loose programming environment or make content hard to read will be rejected in the language design process. Compelling features that are hard to maintain will be similarly _not_ be accepted even at the cost of increased user adoption.

With IT automation in general, there are some (honestly many) theoretical possible cases where disguised 'bad' automation content could be used against a local control system or be used to deceptively automate the deployment of insecure configurations on remote systems, and we want these cases to be more easily caught during automation code review.

To ensure templates are not abused and remain easy to understand, we use the intentionally constrainted _Handlebars_ as the [template system](../playbooks/using-variables.md). Handlebars is a relatively simple templating system that does not allow as much programming capability in templates by design. Further, in Jet, variable expressions are evaluated only once, so it is easier to tell what the value of a variable could be, rather than a possible recursive evaluation where one variable can contain the value from another (potentially several levels deep). It is similarly not possible to insert plugin code at runtime into the jetp runtime environment, such as by including it in an automation content repository.&#x20;

There are also intentionally very few [variable precedence](../playbooks/using-variables.md) rules in Jet so it is easy to understand what values will apply to automation content - _vars_ and _vars\_files_ content in playbooks are always used, _inventory_ variables are next up, and _defaults_ are last.

[Conditional](../playbooks/tasks-and-task-modifiers.md) (if) expressions use handlebars rather than a more fully featured system that has access to many functions. Handlebars conditional statements look a bit like the RPN notation from an HP calculator, but we are ok with that.

There is also no dynamic import feature of instruction lists using a variable path, instead dependent files are listed explicitly in [roles.yml](../playbooks/roles.md), and there is no other include mechanism. Dynamic includes could mean that when auditing automation content the reader would have a hard time predicting what instructions were actually executed by the automation content, especially if a remote system were allowed to decide an include path by using a variable.

Since actions on the local machine could leak information (even by executing 'cat' on files in other directories in verbose mode!) or create symlinks or hardlinks, executing local (control machine) actions during SSH push modes requires an opt-in flag to enable the local action and delegation features (_--allow-localhost-delegation_).

### Cryptography

Jet does not (and will not ever) implement any of it's own cryptographic code and relies on SSL, OS tools, and so on.

In SSH modes, jet _does_ in-fact support the insecure SSH access by password (--ask-login-password, which is there for mostly for home lab bootstrapping), but since SSH passwords can be grabbed from a hacked sshd binary on a compromised remote target, we strongly recommend only using key based authentication in any organizational setting.

For future planetary scale modes, we we will rely on the security features and key infrastructure provided by the supported technologies and vendors.

Our file and copy template operations use SHA-512 to decide when to replace/transfer some files, and there is no other use of these algorithms cryptographically in the main program. This is shared here for those that notice the package dependency and wonder what it is for.

### Secret And Variable Management

Rather than implementing it's own secret management, [jet can load secrets from the environment](security-approach.md#secret-management), allowing interaction with secret management tools such as but not limited to OnePassword's "op run".&#x20;

Many secret management programs will be able to redact the values of secrets that appear in a program (in this case, jetp's) output, preventing the chance of leaking information in normal output, though jet also works to almost completely eliminate accidental leakage risk by only making variables available to the template module and not to playbook templating.

Future modules that take passwords as parameters will have a way to take the variable name as a secret variable as a parameter (coming from the same environment facility) without templating.

### Sudo And Other Access Control Tools

Sudo support in jet allows for configurable execution such that 'sudo' support work with multiple privilege managers other than sudo, using a template pattern where the desired account name is a parameter.&#x20;

{% hint style="info" %}
Sudo access with sudo passwords will be added in the Tech Preview 2 release
{% endhint %}

Because jet modules written in Rust primarily work by executing command line tools on the remote system, it _is_ possible to constrain access via /etc/sudoers to specific commands only for particular remote users - allowing them access, potentially, to only a subject of jet modules and not a run arbitrary commands or arbitrary code with elevated rights.&#x20;

While we do not suspect many users will take advantage of this capability, it will be available and is easy to implement using a sudoers file.

### Shell Usage

To prevent shell command injection, jet filters any arguments sent to the shell involving variables to screen for dangerous characters. This is described in the shell module documentation.

This filtering includes code to prevent spaces in shell arguments to confuse module orders or add additional arguments. The [shell module](../modules/commands.md#understanding-variable-safety) in jet _does_ potentially allow arbitrary execution when variables are used as a parameter to it's 'cmd' argument, but performs strict screening for dangerous shell characters unless an '_unsafe_' parameter is used or no variables are present in the parameters to the module.&#x20;

Still, variables should be used with caution within the 'shell' parameter especially when they are used to form command names. This is true of any automation system. As Jet requires relatively full access to the remote system in order to make _meaningful_ changes, such authors of automation content should be trusted personnel.

### Disk Usage

When using the [file and template](../modules/files.md) module in sudo or sudo-replacement modes, jet must write some temporary files to disk, because SFTP does not allow priveledge escalation. In these situations, we transfer files to \~/.jet/tmp in the (remote) user's home directory (using random temporary filenames) before moving them with sudo (or any sudo replacement) and _never_ use /tmp.

Also in terms of local disk usage, for clarity, Jet _also_ does not have _any_ configuration files, so you always know only the command line options give to Jet affect it's behavior and what Jet will do when executed.

Jet also tries to have a bare minimum number of places where other input files could be, always explicitly set, so it is clear exactly what files are loaded and used.

### Rust

Rust is known for memory safety, making a large class of insecure coding errors to be basically impossible.

The compiler is also extremely advanced so we have extensive error handling awareness forcing all errors produced by functions to be manually considered by the developers and can tell when errors are not handled. Similarly, dead code detection is very robust so we can make sure there is no unused code in the program. Jet intends to always be 100% free of compiler warnings, even on the development branch.

All of this language support allows the code itself to be refactored more aggressively and everything can be designed and modified with a higher degree of intent than if we had to trust in test-driven-development (TDD) coverage percentages to enable refactoring, where we could possibly break error paths and lesser-used features during development.

Rust does use single-binary static-binary builds (with _dynamic_ linking to openssl), though we will always make it easy to build your own version of the package from source, regardless of whether you have any development knowledge of Rust itself.

### Disclaimers, Disclosure, and Security Announcements

Perhaps the most obvious thing -- deploying secure systems is your responsibility, no framework can do it completely for you. You must understand how to secure that which you deploy. We can only make the framework itself protect your data and be predictable. This system will absolutely let you automate the installation of a wide-open database server on the public internet and many other similar things, and Jet won't know you are doing it. Jet provides plumbing and the ability to automate/move content, it does not check the content itself.

As is common for open source projects, none of the above represents any sort of claim, warranty, or guarantee on security features.  **Usage of Jet is always at your own risk**. No claims about security here are guaranteed, they are merely our efforts to set up a good foundation. Open source code does help, however.

We can always make errors. Express any security concerns that may affect users directly to michael@michaeldehaan.net.

Users interested in following security-related announcements (along with general release announcements) can sign up for the _announcements_ subcategory of our newsletter at [https://jetporch.substack.com/](https://jetporch.substack.com).





