# 🔎 Playbook Overview

Jet **Playbooks** select [**Groups**](../inventory/groups.md) from [**Inventory**](broken-reference) and then assign work or configurations to those selected machines.&#x20;

<img src="../.gitbook/assets/file.excalidraw (2) (1).svg" alt="Playbook File Structure" class="gitbook-drawing">





Playbooks can be used to enforce configuration, deploy applications, or orchestrate arbitrary distributed workflows. Playbooks are written in Jet's specific dialect of YAML.

{% hint style="info" %}
**Keeping YAML Friendly**

In contrast to some YAMLOps tools, for example some in the Kubernetes ecosystem, Jet playbooks always strive for a relatively flat YAML structure and human-levels of readability.  We do not deeply nest data structures or include advanced templating features directly in the language, and the document is always valid YAML itself.

We feel a lot of other tools have given YAML a good name, and take a lot of time on language design on purpose.  YAML is, pretty much, the best way to represent structured data that is editable by humans.  However, the simplicity of the language is always in the hands of those building the tools.  While we aren't perfect, we want users to have as little to remember as possible, and try to always make choices as obvious as we can.

YAML tends to have features like interpreting the word "no" as false, in Jet, only true and false are legal for booleans.  Similarly, we never encourage use of some confusing YAML features like anchors.  We do use YAML tags, which may be new to some people, for selecting Jet [modules](broken-reference).
{% endhint %}

Inside of Playbooks, lists of [**Tasks**](tasks-and-task-modifiers.md) define the work or configurations to execute or apply on those selected machines.&#x20;

To encourage reuse and clarity, Tasks can (and usually _should_) be grouped into [**Roles**](roles.md)**.** This way, the Playbook can be mostly thought of as a mapping between Groups and Roles.&#x20;

When using roles consistently, a playbook just answers the question "Who does what work?" and executing a playbook makes that specific work happen -- and playbooks become very concise, with the guts of the automation abstracted away into the role definitions. This is explored further later in this section.

Playbooks are probably best understood when looking at some language examples and following along with the documentation in another browser tab.  See [example content](../basics/example-content.md). For information on how to run a playbook, flip back to the [CLI Instructions](../basics/command-line-usage.md).







