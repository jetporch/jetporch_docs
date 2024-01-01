# ❔ FAQ

Some real questions, some from very real internet humans!

<details>

<summary>Why Did You Create Jet</summary>

[See the homepage blurbs for this one](../).  Jet is being led by Michael DeHaan, the original creator of Ansible. As the saying goes, this is _not_ our first rodeo.

In short, we want to make a free-forever open source automation platform that is fulfilling to use, taking advantage of vast experience in this space, and ensuring automation remains stable and reliable for the next decade, if not longer.&#x20;

Jet should not be a tool you have to deeply study and keep up with, and should be easy to understand, learn, and read. Speed and scaling will be major benefits thanks to Rust and [future plans](../connectivity/agent-mode.md), but we are concerned about ease of use, language clarity, and stability first.

If we do our jobs right, if you write some content in Jet, come back to it years later, and upgrade the program, you shouldn't have to change anything. Of course, it's fun to work on scaling problems and we have no doubt we'll have an absolutely amazing system for that. I guess the answer here is really "both of those reasons".

</details>

<details>

<summary>What Are The Suggested Use Cases Of Jet?</summary>

Many things! Typically, today, automation tools like Jet have a strong role in configuration of classic (non-containerized) systems, stateful data and messaging layers, bare-metal machines, on-premise infrastructure, and in managing the barely considered set of machines I'd like to call the _undercloud._&#x20;

Jet can configure an OS, be used to create a VM image or base image, deploy software, run ad-hoc tasks like applying hotfixes, or just be the world's best ssh-in-a-for-loop command blaster on the planet.&#x20;

While some may have thought the needs for such systems have diminished with the advent of popular immutable systems approaches (ie docker), or even serverless lambda functions, this has  _not_ been the case. Even in container worlds, the supporting management infrastructure supporting it is (suprisingly) more complex than the world that existed before, which _increases_ the needs for Jet, rather than reducing it.

You can probably come up with your own emergent use cases for Jet, such as deploying test workloads to a fleet of machines, bootstrapping arbitrary programs, or writing a script that kicks off other kinds of remote workers. Jet does not care how you use it!

</details>

<details>

<summary>Is This A Port/Fork Of __?</summary>

Jet is about building a new automation system based on vast experience writing and using automation systems, including Michael's _creating_ the one you are probably thinking of! The original code for Jet was written completely from scratch.

We've taken the opportunity to revisit nearly all decisions we made with classic versions of past tools and taken many great alternative approaches in many areas. &#x20;

Rather than call out the specific examples, we encourage you to read the documentation without assumptions about prior systems, and be surprised. Jet should feel familiar, but also refreshingly streamlined and different.

</details>

<details>

<summary>Is Windows Going To Be Supported?</summary>

Sorry, no, the opportunity cost would be too high.&#x20;

We'll go out and say it - there will likely _never_ be Microsoft Windows support in Jet because it would dillute our focus to build excellent automation solutions for Linux, BSD, and Unix-like systems.&#x20;

Unix-like systems have have various features and things in common that make them easy to support in unison with one another. Every second spent on Windows is one not helping users who are not managing windows boxes, with is the vast majority of our audience and we do not want to manage the upkeep and testing of Windows code.  This isn't saying a bad thing about Windows, it's just committing to focus.

We understand people do have to manage Windows machines, but these are usually managed by disjoint teams, so we don't think it is important for one tool to do everything. We do wish Microsoft would develop better tools in this area, and would encourage users to put better pressure on them to do so.

We owe our Linux, Mac, and potential BSD users too much to spend any time on Windows, and adopting Windows pull requests into the tree would be accepting code we do not use or regularly test. We're not saying Windows is bad, we're just saying it is too different and would be like a car garage also serving as a giraffe veternarian, or vice versa. Both are important roles, you would not want the same tools used in both roles.

We mainly develop our Rust code on Macs and we feel /most/ of the IT operations and development professionals are on Macs these days. Jet runs very well from there. We are also open to more modules specifically for Macs.

For those with Windows laptops wishing to only automate Linux and Unix machines, we recommend a virtualized instance of some kind to run jet from -- even if things do work, it is not a tested platform and we can not answer any tickets about Windows control machine problems if they arise, nor we will alter code to make Jet run better from that platform.

</details>

<details>

<summary>What About Support for ___ (Linux/Unix-like) OS or ___ Distribution?</summary>

We are open to patches to add support for any popular not-included Linux distribution, including it's most favored stable package management modules.  [Most are already covered](../community/status.md). If something is not there, don't assume we don't want it, [stop by our Discord](../community/discord-chat.md) and let's chat!

</details>

<details>

<summary>What About Networking Hardware?</summary>

We are open to pointing towards great [external modules](../modules/external-modules.md) that people develop but think these platforms need better intent-based APIs (and standards!) in general.&#x20;

Using Jet, which is designed to manage systems that support Unix-like behaviors, would not neccessarily make sense for networking hardware and would limit the degree of support, attention, and testing we can apply to primary use cases.&#x20;

In both SSH and future planetary scale modes, it would be neccessary to designate certain hosts to serve as jump points to talk to such hardware and we do not feel this really makes sense for the design.

We would like to leave solving network automation up to network professionals to develop new and interesting solutions for network topologies -- independent of where we want to go around managing regular computer systems.  These may look nothing like classic "infrastructure as code" systems at all!

One exception - modules to talk to load balancers are 100% welcome as this is a frequent thing to want to do in the middle of OS automation, provided we can wrap CLI tools that already exist.  This is really easy to maintain the way Jet's modules are implemented. &#x20;

</details>

<details>

<summary>What About Cloud Provisioning / Topology Management?</summary>

Generally, we think purpose-built tools should be used and are eyeing the recent open-source forks of Terraform as well as other tools in this space. Cloud Formation may be more appropriate for some AWS users and may offer some advantages over third party tools.&#x20;

Should these tools develop to support easy integration with Jet or Rust code, we may consider wrapping them to provide cloud topology directly in jetp's YAML (possibly with a new construct other than a play targetting groups of hosts).

We also recognize some automation content may need to interact with a few cloud APIs in the course of updating stateful cloud layers (S3 is one example) and we are open to modules that wrap cloud cli commands where the commands themselves are not otherwise simplistic or declarative enough in the playbook or would benefit from some wrappers to make them more declarative and report on state changes.

</details>

<details>

<summary>How Is This Different Than ___?</summary>

Over the years I've gotten this question a _lot_, and really, we recommend learning from direct experience. Anything else we'd say would be marketing -- we want you to use the tools that are the best fit for you, even if this means not using Jet.&#x20;

This documentation site and examples should give you a good idea of how things are structured and what we value. If you still have a question, [hop by Discord](../community/discord-chat.md).

</details>

<details>

<summary>Are You Planning To Add ___ ?</summary>

Maybe! See the roadmap for immediate plans, but there's a good chance we didn't even think of your idea yet.  Or maybe it's just not on the Roadmap or we thought it was implicit.  [Hop by Discord](../community/discord-chat.md).  We think the best part of open source is all of the ideas coming from everywhere and we're better from learning and hearing from everyone!

</details>

<details>

<summary>What Happened To The Cows?</summary>

They are retired at a nice farm a few counties over from you. You can come and visit them if you like.

</details>

<details>

<summary>Is There Plans For A Commercial ___ ?</summary>

There's nothing wrong with commerical software, but no -- in fact, quite the opposite, we have plans to _never_ create any commercial / closed-source software, ever, regardless of what happens!

We really want everything we develop to be free, and if any "up the stack" future management tools emerge from this project, we also want them to be fully free forever.  The most valuable thing to us is the community that builds around Jet and our own freedom to build what we think is right and we will do nothing to compromise that - which includes not taking venture capital, entertaining buy-outs, or adopting business models that encourage mandatory growth.

We do expect to offer private support for commerical enterprises in the future but are committed to making free tools for everyone to use, share, and develop together. Even this will never impact our mission or limit what we are going to build.

Our original vision was "what if automation was part of something like coreutils" and it doesn't make sense to us to imagine an enterprise version of "/usr/bin/ls" or have some "/usr/bin/ls" wrapper product. That wouldn't make any sense for /usr/bin/ls, and would not make sense here.

We generally think our plans shouldn't require any future GUIs and management software, and will elaborate more on that as new features emerge and plans are revealed.  We are open to seeing what evolves, and may make something great in that space anyway. If we make something it will always be free. If we develop anything new, it will be open source and easy to install in your own environments by a variety of mechanisms. We will never make an open source effort complicated or limited so we can sell a more polished, easier to deploy SaaS version.

</details>

<details>

<summary>Can I Integrate Jet Into My Commercial Product?</summary>

Yes! We suspect many ISVs will want to integrate Jet into their product offerings, and we absolutely welcome that. Having jetp as a reliable asset for the distant future includes being able to use it as part of larger tool suites without fears of license changes, retractions, or price gouging. Our commitment to language stability between versions of Jet should work well in your favor and be beneficial for your customers seeking automation solutions. There are some things you will need to consider to remain compliant with the license.

We do not offer GPLv3 license exceptions or alternative licenses and Jet's copyright is shared among all contributors. If using jet, you will be, by unavoidable definition, using GPLv3 licensed software and need to be aware of the implications to avoid distribution triggers if you allow downloading or installation outside of your SaaS environment, now or in the future, or jet-derivatives run on customer owned infrastructure.

Our integration advice is to call the jet binary built from the _unmodified_ Rust source code and use our log data to understand what jet is doing. Avoid embedding jet's code in a larger Rust program and avoid having patches against the jet source code tree. Not only does this avoid GPL questions, this is also a more stable way to consume the project and will be a requirement of any future "certified vendor" program we offer. If, theoretically, users come into channel asking about modified or stale versions of the jet program, this will rather quickly result in potential confusion we wish to avoid as we cannot support questions about derivative programs.

To make things easier, we can also clarify that _external_ (non-Rust) modules and CLI-invocation are not considered linkage or distribution for GPL purposes. Inventory scripts copied from 'jeti' may have their own specific licenses and executing of in-house inventory scripts are not considered linkage either - if you modify them and distribute a GPL inventory script, release the code for them. With all of this considered, the license should really not be a concern for any well-meaning integration that builds itself around invoking the jetp binary and using log data.

In addition to license questions, we also really want to support vendors building integrations so all consumers of jet get a great experience. There will likely be ISV-tier support & an approved vendor certification added once a support offering is launched, that we would strongly encourage all ISVs to explore it. Knowing your business and product means we can make sure the tools we build can continue to work well in those environments and be able to plan for the needs of your customers.  [Contact us if you would like to explore details](mailto:michael@michaeldehaan.net).

</details>

<details>

<summary>How Can I Contribute?</summary>

[See this guide over here!](../community/contributing.md)

</details>

<details>

<summary>How Can I Get In Touch?</summary>

If you have a sensitive question that you'd rather not share on Discord or cannot join Discord for some reason related to work security preferences, or perhaps you just want to invite us to a podcast (thanks!), you can [email Michael ](mailto:michael@michaeldehaan.net)directly.  We look forward to hearing from you!

</details>



