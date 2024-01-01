# 💻 Groups

Before we talk about how to define groups later in this chapter, let's understand what Groups are.

Groups map [Hosts](hosts.md) _- manageable machines specified by hostnames or IPs -_ into sets of machines that can be given particular orders at the same time.&#x20;



<img src="../.gitbook/assets/file.excalidraw (1) (1) (1) (1) (1).svg" alt="Groups Organize Hosts" class="gitbook-drawing">





Some ideas to think about:

* Groups do _not_ need to specify that all systems in a group are alike, whether that means their roles or operating systems.
* Groups typically represent something having a common location (like a datacenter, region, or availability zone), property (hardware type), or function that would warrant special attention or variance in configuring machines in that group.&#x20;
* Groups can overlap, with a system being in more than one group.  Any amount is fine.&#x20;
* Groups can have subgroups, where they automatically include all the systems in child groups below them. Subgroups can continue to arbitrary levels of depth.
* Group hierarchies do not have to represent a tree, but do represent a graph.  The graph technically can have cycles, but Jet will not get caught in any infinite loops.  Cycles can sometimes result in some unexpected variable precedence behavior. Try to avoid cycles.
* Groups can have variables assigned to them that apply to all hosts in the group, and these variables can be used in templates and the Jet playbook language. Sometimes a group will never be addressed in configuration, but is there to only apply certain variables.

Examples of groups names might be things like "us-east-1", "testlab", "build-nodes", or "webservers".&#x20;

These concepts will be explored as this chapter continues, and more so in the [Playbooks](broken-reference) section.



