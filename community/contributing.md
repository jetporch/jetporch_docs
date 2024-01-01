# 🚁 Contributing

Welcome!&#x20;

Thanks very much for interest in contributing to Jet, we are happy to have you here! This page serves as a guide for understanding how to work with the project. [Development topics](broken-reference) are covered later on in the documentation -- this chapter is more process and philosophy related but is equally important.&#x20;

You may also want to read our writeup on our use of [SourceHut](sourcehut.md), but be sure to read the relevant sections of this guide before submitting a patch or filing a ticket.

## Commentary

<details>

<summary>Balancing Growth And Focus</summary>

If you would like to contribute to Jet (thanks!!!), we want you to know how we expect things will play out.&#x20;

We work differently than many GitHub-first projects you may have worked on in the past, as we are designing our community workflow to enable high contribution volumes and _long term_ maintenance of all contributions. We have a lot of experience in this from past projects.

It is important to underscore that Jet is foremost an open community project and will always be _greatly_ appreciative of all contributions. At the same time, Jet wants to have a professional and robust "flavor" (hopefully this is apparent!) and will always seek to retain and strengthen that commitment even at the expense of greater adoption or features.

Therefore, development of Jet must balance these two forces and not succumb to a desire for feature-sprawl or popularity at the expense of usability and quality. As such, the standard for successful merging of code additions will not be impossibly high, but may be significantly higher than some other projects.

While we believe that many features will be easy to develop based on the architecture of Jet, it's also true that sometimes simple things become more involved than they first appear, because they must be done a certain way and to a certain degree of completeness, clarity, consistency, and attention to detail. We do this to plan for future development and upkeep - also realizing every accepted feature comes with an upkeep cost.

We want to look at open source software as building an architecture of _surprise_ and _possibility_ and making a "fun" community to be a part of, but also look at our job as being stewards of the project's original and constant goals. The program's code and quality will always matter the most, more so than the temporary good feeling of getting a pull request merged. This balance runs through _everything_ we do.

As it is important to preserve the minimalism, security focus, and attention to detail of Jet, this may mean saying "no" more often than some may expect or want. We want to consider nearly all ideas and grateful for all of them. When we decline an idea, it's not about the idea or some code being uninteresting or inappropriate per se, but the larger picture of long-term upkeep needs and surface area -- and sometimes about learning experience.&#x20;

If deciding to not implement some idea or take some code contribution, we'll always explain our reasons and can be persuaded in many cases to change our mind. However we can seldom _ever_ persuaded to break existing users or do something that will potentially enable hard-to-maintain automation content, even when that feature is powerful and exciting.

If potential contributors understand these factors going in, we should have a great experience working together, and will always work hard to make that a rewarding experience.

</details>

<details>

<summary>Simplicity Of Process / Commitment To Contributors</summary>

We do not have any governance boards, committees, steering groups, votes, checklists, need for approvals on PRs, CLAs, bots (we are _very_ much anti-bot), scheduled meetings, meetups, conventions, working groups, employees, or language feature review processes. It's really just this - talk about things on chat first, do good work, always make sure we are doing the best possible thing, and plan for the future - so not that bad really!

Servant leadership is very important to us. We therefore aim to keep our bug queue short and well managed, and not leave people waiting for months in between trying to help out with an idea. We will favor getting things done over bureaucracy and formal process at all times.

</details>

## How To

<details>

<summary>How To Submit Bug Reports</summary>

[Bug reports should be submitted on our SourceHut tracker](https://todo.sr.ht/\~mpdehaan/jetporch). You can create a free login to use SourceHut and do not need the paid login that you need for project hosting.

If you are not sure if something is a bug, you are welcome to stop by [Discord](discord-chat.md) to discuss it, but this is not required.

Do not assume any short replies - which we'll try very much to avoid wherever we can - are anything other than us being busy. If you have any problems, feel free to reach us on chat - especially if you disagree with one of our conclusions or something remains a problem or you disagree with a ticket being closed. We may not always see notifications, especially on closed tickets, so stopping by chat is often helpful.&#x20;

Thank you very much and we greatly appreciate all reports!

</details>

<details>

<summary>How To Ask User / Support Questions</summary>

Regardless of your question or problem, we want to help you out.

The best place to ask questions is **Discord**, so please do not ask user/help-related questions in the tracker as you'll be directed to Discord.&#x20;

Should questions or communities arise on other forums (serverfault, reddit, etc), we will not have time to participate there.&#x20;

The ticket tracker is unfortunately _not_ a good place for involved discussion and we don't want to frustrate anyone by closing unresolved questions or frustrate ourselves on having a large queue of questions that we don't know when to close mixed in with other tickets.  Everything we do is optimized around keeping the tracker queue well managed.

A paid email support option for companies most likely will be available at a later date in the coming months, but does not currently exist (and we're too new right now anyway). Best-effort chat support will always be offered for free no matter what happens. We can't really manage one on one free email support, and not wish to manage a mailing list (nor do most people want to join one), and would like everybody to be able to see (and help answer) questions. &#x20;

If questions come up frequently in Discord chat, we will always try to work those answers into the documentation to minimize future questions so that we can continue to scale - and less questions have to be asked!

Thank you and if you're willing to[ join chat](discord-chat.md) we'll get you sorted out!

</details>

<details>

<summary>How To Submit Patches Bug Fixes</summary>

Thanks very much for wanting to fix something!  If you would like to submit a change for an obvious bug, [see our SourceHut instructions](sourcehut.md).

Try to keep code changes minimal, clean, and non-invasive as best you can, please do not refactor or restructure code in other areas of the program unrelated to the bug fix, even though  you may very much want to. The simpler and cleaner a patch is, the quicker we can review it and see about getting it applied. We'll say it again elsewhere, but also please **turn off auto-formatting in your code editor.**&#x20;

Please be sure the code remains free of warnings and handles all possible errors appropriately as explained in the development guide. We really don't want to see warnings when we fire up the compiler! Your thorough testing of your code on affected platforms is required to also be respectful of our time.

If there is code you don't understand, we recommend asking questions on Discord (git questions are fine too!), and we'd be happy to explain absolutely anything about the source.  The more people who understand Jet's code the better!

</details>

<details>

<summary>How To Submit or Discuss Feature Ideas / Requests</summary>

Ideas are very very welcome and we basically live for idea discussion!  **Stop by Discord** and we want to talk about your idea.&#x20;

To keep our TODO lists clean, we do not accept or record requests-for-enhancement (RFEs) on the tracker. The tracker is for bug reports and pull reuests only. Ideas may either get added to the Trello [roadmap](roadmap.md) or if you are considering contributing a feature, we can discuss what shape it might take.  All planned features are also not _necessarily_ on the roadmap. Also, because we don't do something today doesn't mean we might not decide to do it tomorrow!

It may happen that we may say no to an idea to keep things focused in the problem or because we don't want to maintain some code. In that case, external modules may be a good fit if the idea was module related.  If we're not super interested in a good idea, it may be there other priority tasks for us to work on at the time but it would still make a good contribution item for you to work on.

</details>

<details>

<summary>How To Submit Patches For Features</summary>

[Patches are accepted using SourceHut and git-send-email](sourcehut.md).  Read this page for details, but also the following important information:

Everybody loves features and we want to hear your ideas! To be respectful of everyone's development time and particularly yours, we suggest not sending a patch for a feature without first **discussing it on Discord** prior to working on it, at least minimally. This includes module submissions to see if they are a good fit for Jet or not.

Why do this? We want to make sure features are a good fit for the program and also don't want to have to describe how we might want something reworked if we can instead talk first about how to build it a certain way the first time. This saves everyone work across the board. We also want to help avoid duplicate efforts when we know something may already be in progress or have particular plans for it.

Be sure to make sure your submission handles every possible error or input/output scenario correctly on all possible platforms. Submissions should not be proof of concepts and should aim at being relatively impervious to error conditions.

We know the feeling of sending in some code only to get it declared unwanted or to be asked for tons of changes and never get it merged or that talking weeks - those are all terrible things. We're seeking to avoid that as much as we can.  We absolutely don't want to hurt anyone's feelings by leaving their pull request sitting in limbo for two months while constantly asking for it to be re-based. Nothing is fun about that - up-front conversations are great and make things easier!

We very much look forward to working with you! &#x20;

</details>

<details>

<summary>How To Submit Documentation Errors, Comments, or Changes</summary>

Suggestions for changes to the documentation should most likely be mentioned on Discord, but documentation bugs can also be [shared on the tracker](contributing.md#how-to-submit-bug-reports).

Documentation is maintained in GitBook and it is not possible to submit a change to the documentation via pull request directly since GitBook synchronization is not bi-directional, and we've chosen to use their web editing features to make it easier for us to write more and better documentation faster.&#x20;

This is a little bit of a weird thing, but ultimately GitBook is a huge win for the project over other documentation systems. If you are trying to add a suggestion for some new docs, feel free to write up some of the suggested text and send it to us in a ticket. You don't get any commit log references but we will appreciate you just as much.

</details>

## Other Topics

<details>

<summary>I Don't Know What I Want To Do, But I Want To Help?</summary>

If you want to help work on something and don't know what to work on, unfortunately, we can't really help with that too much -- but we do want you to get involved! It's a frequent question in many projects, and while we appreciate the interest, we can not assign tickets for people to work on.

The best open source additions are for things that you want and need. If there are an open and unassigned bugs, this might be a good target, or perhaps you could try your hand at writing a module for your favorite operating system... but please stop by Discord first to see if the idea would be a good fit, as we want to be respectful of everyone's time and don't want to reject anything after the work is done, because as mentioned above, that's a terrible feeling for everyone. If you have a particular need, scratching your own personal itches are always the best contributions!

If you want to do something that we for some reason do not want to include, external modules will fill a strong void for when someone wants a module and we don't think it woul d be a good fit for the core program. The whole idea is to enable that possibility so the system remains open ended while still having strong opinions.

</details>

<details>

<summary>Avoiding Trivial or Unwanted Commits</summary>

Hopefully this doesn't apply to anyone reading this, but we are sharing this after much experience running some very active projects in the past.  Now that the project is hosted on [SourceHut](sourcehut.md), we anticipate we do not get very many of these commits since gamification is more a problem for GitHub, but these items still apply.

Please do not submit any of the following as they will be closed, and they take valuable time away from the product's development and communications with serious users:

* Code reformatting changes - this breaks attribution in commits and often impairs readability.  While many may strongly believe in their own preferences, _all preferences are subjective_. Code formatting will be discussed in the development guide. Please turn off all auto-formatting in your editor. Debates about coding style are not productive.
* Trivial spelling corrections - we'll fix them _probably_, but we're also unlikely to merge them and would prefer you engage with the project in a more serious way.  This takes time away from other more important development work and merging other PRs.
* Results from auto-linters or correction programs (including opinions from clippy).
* Refactoring issues that are matter of stylistic or personal preference, unless discussed first.  This does not include fixing our Rust code where you can explain why the fix is functionally better/useful and simpler, but we still recommend discussing it in chat first. Code changes always require lots of extra testing. We don't mind learning new tricks - but are concerned about readability, so more concise methods are not always better (this may include things like using _map\_err_ instead of _match_, etc). We may have reasons for preferring the particular way something is done and find it more readable.  Code that must be unrolled to be worked on and then reassembled is almost always worse, and it gets tiring explaining choices when denying pull requests of this nature.
* Introduction of new library dependencies, unless discussed first - we're probably open to things, but want to make sure we understand all the things used, and may ask for something to be implemented a different way.  An active maintaince history is very important, and each new dependency can be a liability.  It all depends on how easy it is to remove and whether it affects the user surface area of the program if it were to be removed. The more invasive a dependency is or the harder it would be to replace increases the likelihood that we would not want to include it. Dependencies must generally have a large followings and a stable maintaince history to merit adoption.
* Introduction of async in Rust beyond the existing usage of rayon
* Changes to .gitignore files or other dotfile additions for your editor, personal tools, or favorite build systems that clutter the repo root.
* Any code you did not test (including error paths), breaks compilation, or adds warnings.

</details>

<details>

<summary>Copyright And Ownership</summary>

All files should have the GPLv3 license header.  The correct copyright for all new files is:

**(C) 2023, Jetporch project contributors**

In short, everyone will always own their contributions and Jet is collectively owned by everyone who works on it. Do not put your name on any file, especially when making modifications to one. All pre-release files with original history may have Michael's name also attached but that is unimportant. (Also, don't change these, basically just leave copyrights alone and less us handle that).

This distinction is a bit weird, I know. It's important that JetPorch is owned by everyone, but no one has exclusive copyright to a file that could decide to relicense a file. Thus, the copyright being assigned explicitly to everyone. By having a original files also explicitly owned by the author, it's impossible that no one be able to claim that they represent "everyone" since there's an explicitly named person that would always say no.  The result is the project is licensed locked forever at GPL v3.0 "or a later version".&#x20;

To generate the full list of authors who have contributed, from a git checkout, run "_**git shortlog -sne --all**_", or "_**make contributors**_"

The license and copyright choices are intentionally designed to make it impossible to change the project away from the GPL license. By this we mean it would be impossible to get every contributor to agree to a license change, nor would we ourselves ever agree. &#x20;

In short, Jetporch remains open and free, 100% guaranteed, and no one can remove a feature to sell something later -- we would view that as a betrayal of everyone that has put faith in the project.There is no contributors license agreement nor any intent to have one. We also commit to never removing an open source feature to make a commercial version of it.

Authors submit contributions on GitHub with the implied assertion they have the ability to submit code unencumbered by any employer preferences or patents, they have rights to their contributions, and they are not submitting code plagiarized from other sources without attribution or in conflict with the GPLv3 license of the program.

</details>

###



















