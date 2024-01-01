---
description: the Jet Enterprise Professional Orchestrator, aka Jet
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: false
  pagination:
    visible: true
---

# JetPorch

Jet is **a general-purpose, community-driven IT automation platform** for configuration, deployment, orchestration, patching, and arbitrary task execution workflows. Jet is for Linux and Mac systems.





<img src=".gitbook/assets/file.excalidraw (1) (1) (1) (1).svg" alt="" class="gitbook-drawing">



Jet features:

* A strong emphasis on language simplicity, consistency, and stability with a minimal aesthetic
* _Extremely_ fast Rust-powered multithreaded, parallel SSH, push-based configuration
* Optional, agent-based pull-framework (ETA: end of year 2023)
* Static HTML-based run reports (ETA: end of year 2023)
* An enterprise security and audit focus
* Friendly, easy-to-read documentation&#x20;

Jet is being lead by Michael DeHaan, the original creator of popular IT automation programs _Cobbler_ and _Ansible_.&#x20;

<figure><img src=".gitbook/assets/jet1.png" alt=""><figcaption></figcaption></figure>

Jet's engine and included modules are implemented in Rust though soon user modules will be able to be implemented in any language that can speak JSON. To keep up with the project (or get involved), see the [community](community/discord-chat.md) [links](community/blog-and-announcements.md) in the sidebar!&#x20;

### The Basics

<img src=".gitbook/assets/file.excalidraw (2).svg" alt="" class="gitbook-drawing">

### Additional Summary Info

{% tabs %}
{% tab title="Language Design" %}
To make things easy for IT users with Ansible familiarity, jet uses a **YAML** dialect similar to the Ansible **playbook** language. While our playbook language is different than classic Ansible, an analogy might be to consider UK vs American English. _Glacier! Aluminum! Schedule!_

**Templating** also uses a familiar bracket syntax, this time using _Handlebars_ as the template engine, so porting content should remain straightforward. We have also made variable handling predictable by having less places to define variables.

We want the Jet language to be very stable to avoid any needed content changes during user upgrades. As such, the language documentation will be treated as a **specification**. Wherever possible, we will be deliberate in the design process to eliminate any  breaking changes to the existing user automation content.&#x20;
{% endtab %}

{% tab title="Performance And Capabilities" %}
Jet is implemented in **Rust**, producing efficient native binaries cable of **massive parallelism.** Rust's compiler also checks code to an _extremely_ aggressive degree due to the best-in-class type system, greatly benefitting code quality. Similarly, Jet's code itself is designed to check automation parameters aggressively to avoid runtime surprises and common human errors.&#x20;

While the engine is Rust, Jet's native modules rely on shell access alone on remote systems in terms of what they execute. No dynamic language runtimes need to be installed on remote systems. However, through external module features, Jet will still be able to execute JSON-based modules written in other languages.

Jet's supports **local configuration** and multithreaded parallel **SSH**. An agent based pull mode is a pending feature.

Additional modules and language features will be added over time.
{% endtab %}

{% tab title="But Wait, There's More" %}
Tons of features not mentioned here are scattered throughout the documentation site. Feel free to explore the various chapters. You may also get a good idea of the feel of Jet by looking at some [example playbook content](basics/example-content.md).  If you have any questions, [stop by chat](community/discord-chat.md) and we'd be glad to talk with you about almost anything!
{% endtab %}
{% endtabs %}

### Legal/Notes

_Jetporch (also referred to herein as Jet) is a GPLv3 licensed, community-developed open source program. Jetporch is unaffiliated with Ansible or Red Hat Inc. Ansible is a registered trademark of Red Hat Inc. and is also a GPLv3 licensed open source application._&#x20;

_Jetporch does not come with a warranty, expressed or implied. Usage is always at your own risk._
