---
description: This page is a placeholder
---

# 🔎 Module Overview

Modules are the 'behavior' in Playbook [Task](../playbooks/tasks-and-task-modifiers.md) statements that cause meaningful action to happen on managed systems- they do things or make some property of a system match a particular state.

Tasks appear like so in a Playbook:



```
tasks:

   - !shell
     name: optional comment about running /usr/bin/foo
     cmd: /usr/bin/foo --flag1 --flag2
     
```

This diagram was used earlier in the documentation, but we'll repeat it here:



<img src="../.gitbook/assets/file.excalidraw (1) (1).svg" alt="" class="gitbook-drawing">





Core modules are code that is part of our Rust binary and do not require any software to be installed or transferred to remote systems. We will follow a many-batteries-included philosophy with some minor curation caveats that are detailed on each module page.



<img src="../.gitbook/assets/file.excalidraw (15).svg" alt="" class="gitbook-drawing">





[External module support](external-modules.md) (pending) involves executing modules written in any language that can read or emit JSON, which can also be used to call modules from other management tooling, regardless of programming language implementation choices.

