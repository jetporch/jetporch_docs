# 🛠 Module Development

is chapter is about developing [modules](broken-reference) for contribution into jetp. &#x20;

## When To Develop Built-In Modules Vs External Modules

It is important to understand internal modules are part of the core program and are not exactly plugins but are very intentionally baked _into_ the program. It's very hard to break a module, even a seldom used one, by making a code change elsewhere in the program. While modules are baked in, they are however still very _modular_ in that they all have a common interface (traits) and ways of working.

The purpose of a Jet Rust module is to provide an extremely fast, tight, and accurate implementation of a OS/configuration/deployment capability that is instantly available to all users of Jet _without_ searching, downloading, or thinking about it.  This may be considered a 'batteries included' philosophy.

For modules that may be a bit more site-specific, such as hitting your own internal APIs, [External Modules](../modules/external-modules.md) will be a faster means of development that allow you to work in any language. It will be very difficult to maintain a fork of Jet, and it is also frankly _slower_ to write modules in Jet properly, but we like it this way. &#x20;

Generally, we mostly want to consider new module contributions particularly in areas of:

* Increasing the quality of support for configuring and working with operating systems, including services and package managers
* Suppporting general  purpose automation needs and extremely commonly used functions

We do not currently include modules for:

* Cloud topology configuration, though simple operations useful in OS configuration like downloading from buckets and such are fair game.
* Talking to new/particular SaaS services that are likely to frequently change APIs

With many exceptions, modules typically like to work by wrapping command line tools with stable interfaces.  The emphasis here is upon _**stable**_ which is the critera for inclusion.

For instance, suppose we wanted to make a module for downloading content from a FooCloud object store and FooCloud was already used by 25% of the world.  This makes it a pretty good idea to include support for FooCloud!

What we would want to do is see if FooCloud has a CLI tool.  If the CLI is already great, we might not need a module at all. However, if the CLI is hard to use, requires multiple steps, or is not declarative in nature (such as the 'get' commands being different from 'update') it may be a good fit for module development because having a module would improve our quality of life in writing repeated automation tasks around these items.

As another example, assume we have a new monitoring startup company that only has an API that is available via submitting rapidly evolving JSON to a web service endpoint.  Because this is both a new and small company, as well as there not being a stable command line tool to interface with the program, it does not make sense to add a module for Jet.  Should this monitoring company evolve to become widely used, a module might make sense provided there was a _**stable**_ command line tool. Because the tool would be ideally be frequently updated, there is no concern about forever maintaining API interfaces with the vendor.

Controlling module quality is important to Jet while also maintaining a "batteries included" feel.  Since modules are written in Rust, we have strong guarantees that they will be maintained correctly when written correctly, and the various details of the finite state machine underpinning modules, as well as internal API design, ensures these can remain high quality.

Inclusion of a module is always a discussion, see [Contributing](../community/contributing.md) for some rationale about that and how to discuss module ideas on chat.

With that out of the way, let's get to details!

## Identifying An Exemplar Module

While the API contracts of modules will ensure mostly correct implementation, you will want to identify a module that is closest in nature to the module you want to develop.

For modules working with files on the local filesystem, this may be _**template**_ or _**copy**_ or _**file**_. Realistically, you should study all three, and more so.  There is something to be learned from any module's implementation. These live in modules/ in the git checkout.

For adding support for a package manager - or most modules that mostly wrap an OS command and provide declarative/idempotent state to an imperative command, a good example module to look at is **dnf** or **apt.**

### Early Steps

Create a new file for your module in src/modules/foo.rs, replacing foo with the name of your module. &#x20;

Consider copying the code from an exemplar module and adapting it.  Think about what steps it would need to do to fulfill the "query", "create", "modify", "remove", and/or execute legs of the state machine from the module code you are looking at.

Read the steps below to learn more about completing the module and wiring it in to jetp.

### Adding A Module To The Module Registry

This is a tiny bit weird. &#x20;

Because of some interplay between polymorphic dispatch macros, our YAML serializer, and Rust's rules about data-inheritance in structs (which does not exist currently), we have a tiny bit more boilerplate to wire modules into the "module registry" than what we would like.  This will possibly improve in the future with more build magic, but please forgive this and you'll quickly be in happy territory in just a few minutes!&#x20;

1. Open modules/registry/list.rs and search for "Echo".  This shows you every occurance of the Echo module being added to the registry.
2. Add the name of your module to the list everywhere you see Echo, but keep things alphabetized everywhere to keep the system neat.  There are currently only four places you have to add this - and they are each very simple one-line entries
3. Open src/modules/(category)/mod.rs and add your module import there, and also keep it alphabetized.

Your module is now part of the Rust code and will be embedded in the single-binary that compilation produces.  Of course, it won't compile yet, most likely, so let's dive into the individual parts of module development in greater detail!

### Implementing Your Module

Using your exemplar module for reference, module implemntation is best understood by looking at the module code in various stages, and here we'll explain the design decisions of one particular module, in this case _**dnf**_ ... modules/package/dnf.rs

{% hint style="info" %}
A GitHub link will be inserted here after release
{% endhint %}

The dnf module wraps various OS commands to provide a declarative layer over the package manager.  While dnf is specific to certain platforms, you will be able to learn from it just the same.&#x20;

Each module has some normal structure, though the details of each module may be very different:



<img src="../.gitbook/assets/file.excalidraw (1).svg" alt="" class="gitbook-drawing">

### Module Name

```
const MODULE: &str = "dnf";
```

The name of the module is only used when there is no 'name' element on a task in the playbook, and this is shown to the user in this case.  The "!tag" name of the module instead comes from the name used in the enum in registry/list.rs, but these should match.



### Inputs And Outputs



```
pub struct DnfTask {
    pub name: Option<String>,
    pub package: String,
    pub version: Option<String>,
    pub update: Option<String>,
    pub remove: Option<String>,
    pub with: Option<PreLogicInput>,
    pub and: Option<PostLogicInput>
}

struct DnfAction {
    pub package: String,
    pub version: Option<String>,
    pub update: bool,
    pub remove: bool,
}

```

Every module will define a Task struct and an Action struct.  The Task struct corresponds to the input in the YAML playbook.  The Action struct is the rendered and evaluated version of the Task, after substituting all template variables, casting all parameters, and so on.

This is done by the evaluate function:

```

fn evaluate(&self, 
    handle: &Arc<TaskHandle>, 
    request: &Arc<TaskRequest>, 
    tm: TemplateMode) -> Result<EvaluatedTask, Arc<TaskResponse>> 
```

The evaluation code for every module will be different, but it doesn't actually execute any behaviors on the remote system, it merely checks and evaluates all of the arguments.  The various features used on each parameter are mostly from the "handle.template" namespace, so browsing src/handle/template.rs will give a good idea of what is offered here.

Handle is an object that basically a power tool or swiss army knife that modules will work with extensively, here, it's mostly "handle.template" or "handle.repsonse" that is used here.  Refer to the source code for specifics.

Request is the request object, which is needed to construct a response. Later down the module will perform different types of actions based on the type of request, but for evaluate, the request object is mostly uninteresting.&#x20;

tm (TemplateMode) is mostly an internalism you don't need to think about. 99% of the time, the value of this is "Strict", which means all variables in an object need to be defined. There are some ways this is used internally ahead-of-time in the call chain, and that's not really important for module authors to  understand.

### Getting Things Done

When the 'main' piece of the code is walking tasks, it will call evaluate on each to produce an action, and then on each action, it will call dispatch.

Dispatch in each module looks like this:



```
fn dispatch(&self, 
   handle: &Arc<TaskHandle>, 
   request: &Arc<TaskRequest>) -> Result<Arc<TaskResponse>, Arc<TaskResponse>>
```



And each module will perform different actions based on request.request\_type, returning different variations of return objects from handle.response.

There is a finite state machine behind the scenes that will ensure certain classes of responses are not allowed for certain types of requests.

This is best understood by browsing various modules.  In the case of dnf, we have responses that occur for varying behaviors:





<img src="../.gitbook/assets/file.excalidraw (16).svg" alt="" class="gitbook-drawing">



|        |                                                                                                                                      |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| Query  | return what type of action should occur based on the current state of the system                                                     |
| Create | the resource was missing, so perform these actions to create it from scratch.  In the case of packages, create means 'install'       |
| Modify | the resource did not match the specification, so perform some actions to modify it.  In the case of packages, modify means 'upgrade' |
| Remove | the resource exists but should not.  In the case of packages, remove means 'uninstall'.                                              |



In addition some additional categories are not used by the package module but appear elsewhere:



|         |                                                                                                                                                |
| ------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| Passive | used by modules that perform some action that does not really change the remote system, and may only provide some output or set some variables |
| Execute | used by modules that run commands                                                                                                              |

Modules are to return errors if they are called to perform actions they cannot support, but this will also not happen, because the query module will not return transitions into those states.

