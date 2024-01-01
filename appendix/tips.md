# 💡 Tips

## Basics



<details>

<summary>Tips For Learning And Adopting Jet</summary>

When learning or adopting any automation system the usual advice to write automation content for one service at a time, learn the tool as you learn what you need to deploy that particular service, and expand gradually.

Starting with the [automation example content in our GitHub repo](https://github.com/jetporch/jetporch\_examples) is a good way to become familiar with the system. If you have any Jet questions or encounter any challenges, you can stop by our [Discord](../community/discord-chat.md) and we would be happy to help. &#x20;

There are places where many features you are seeking may not be yet available (yet?), and this does not mean they aren't things we want to add, so please express any thoughts about missing functionality especially if you do not see it on the [roadmap](../community/roadmap.md).  There may be alternative solutions to many workflows.

Documentation here is mostly organized in a random access way that encourages learning something when you need it. Regardless, be sure to understand concepts about [playbooks](../playbooks/plays.md), [tasks](../playbooks/tasks-and-task-modifiers.md), handlers, roles, [variables](../playbooks/using-variables.md), templates, and (for SSH modes) [inventory](broken-reference). Understanding all of the modules is definitely not required.  Following along with example content (soon to be on GitHub) is definitely recommended.

Learning in a lab or VM environment is probably easiest first with a few VMs and using [static inventory](../inventory/file-based-inventory.md) with IP addresses or hostnames. You can also use your local Mac or Linux computer.  Using verbose mode "-v -v" may be instructive for  understanding how some Jet commands operate under the hood.  If you want to see the value of variables, you can use [echo](../modules/control-flow.md) or ['jetp show](../basics/command-line-usage.md)'.

Once you are ready to get going, for users with cloud systems, we recommend using '[jetp ssh](../connectivity/ssh-mode.md)' using our [dynamic inventory scripts](../inventory/dynamic-cloud-inventory.md) which is compatible with any of the external inventory scripts you can download from Ansible. We may have our own versions of some of these scripts in the future. External inventory scripts can easily be tested with '_jetp show --inventory script.py --groups all_'.&#x20;

The future planetary scale features are designed for very large install bases, and really will not be needed for many users with medium to even large sized installations. It's ok to start with basic SSH now as our Rust-based platform should offer some very interesting performance capabilities on it's own.

</details>

## Process And Organization



<details>

<summary>Integration with CI/CD Pipelines and Build System Tooling</summary>

Most users will want to gate the execution of their automation using some sort of build system tool.

This is more desirable than initiating configuration from individual admin machines and can prevent problems when multiple admins are running multiple steps at once.  Further, it gives centralized logs and red/green status to whether a deployment encountered any errors.

Look for whether your build system tool has a plugin to work with SSH credentials and can effectively execute SSH-agent on your behalf.&#x20;

If the build system supports parameterized jobs (i.e. "surveys") you can pass the results of those parameters via JSON or YAML to the "-e" command, for instance, you might have a manual job that asks for a release version.

Read the [Secret Management](../playbooks/managing-secrets.md) chapter and see how Jet can be used with secret management tools to keep secrets out of build logs.

</details>

<details>

<summary>Handling Environmental Differences</summary>

Often different environments will use different servers and these configurations must be baked into the configurations of various machines in configuration files.

When this occurs, using group variables on disk, in a group\_vars/ folder alongside your inventory is most likely the best way to set these settings so that they are correct regardless of what playbooks or roles are used.

For cloud based inventory sources, you may want to use tag based groups to apply these variables to particular hosts.

If there are further questions about this, [stop by the Discord](../community/discord-chat.md) and we'd be happy to discuss details!

</details>

<details>

<summary>Mixing With Other Automation Systems</summary>

It is quite common for many organizations to have multiple automation systems, particularly when migrating between them, or when different departments make different technology choices.  This is completely acceptable.

Using Jet to call other automation tools is fine and should not present any problems, as long as the remote tools remain non-interactive and finish in a short period of time. If doing this, you may wish to execute them with "at" to make them run asynchronously, and use another playbook to collect logs. You will not get intermediate output while long configuration processes are running.

It's also fine to  use another tool to execute Jet in local mode if you so wish, or even executing jet locally on first boot.

</details>



## Performance And Scaling



<details>

<summary>Increasing the --threads Value</summary>

The default value for SSH multithreading in Jet is 20 threads, most capable control machines should be happy with more than 100 or more.  Experiment and see what works best for you.

</details>

<details>

<summary>Running Configuration From Inside The Cloud</summary>

Suppose you have a cloud environment with N number of systems in it that we want to configure as quickly as possible.  While Jet is smart and tries to keep connections open, it's also true that it would be nice to be as close to those systems as possible.

You will get better performance from running configuration tasks from a bastion host or designated "helper" machine inside your cloud environments than from a "work from home" setup where each connection comes in from outside the cloud. Being mindful of this will maximize the performance of Jet and result in configuraton runs completing even faster

</details>

<details>

<summary>Use of Mirrors</summary>

When using a configuration management system that has the ability to address thousands of machines at once, it is important to be respectful of public infrastructure the machines may be addressing.

Do not use machines to download from public resources like pypi on a multitude of machines at once.  Instead, look to tools like Artifactory to create private mirrors.

This will also help you avoid potential problems when upstream resources are inaccessible, packages are removed, and so on.

Where public resources are being accessed, you can configure --batch-size in Jet to address a small number of machines at a time that will not be effectively be working towards DDOSing a SaaS service or community resource.

</details>

