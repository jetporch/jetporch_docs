# 🛖 SourceHut

Jet will be moving code and collaboration to SourceHut in the very near future.&#x20;

This page is a temporary page to include some writeups for testing before making this move final and replacing our GitHub instructions.GitHub will remain as a read-only mirror in this case, where we disable issues (moving to the SourceHut tracker).

This page is expected to be a little rough at the moment and will be edited as we get some feedback from folks testing this workflow.

## Important Web Links

* Main project - [https://sr.ht/\~mpdehaan/jetporch/](https://sr.ht/\~mpdehaan/jetporch/)
* See patch queue here, subscription is not required for submissions - [https://lists.sr.ht/\~mpdehaan/jetporch-devel](https://lists.sr.ht/\~mpdehaan/jetporch-devel)&#x20;

## Why SourceHut?

Jet will be using SourceHut to simplify workflow in merging patches from both maintainers and contributors. SourceHut uses a similar patch submission process as the Linux kernel.

For those that remember pre-GitHub times, things were actually easier in many ways because you did not have to use a web interface and juggle many different copies of remote and local branches, a local branch and an email was all that was needed to collaborate. Over time, GitHub became all people knew, and these elegant weapons from a more civilized age were lost in time.

For maintainers, we had more control of merging. Lately, GitHub has been making errors in attribution of some patches, and we do not like how it encourages 'web merging' pull requests and makes the manual process - which can involve more attention and manual testing more difficult. Further, we oppose some AI related training done on projects without their consent or regard for license compatibility, and fear what lazy reliance on AI assisted code will do to the industry.

Often to use GitHub well we have to ask contributors to rebase or re-squash a patch, which requires an intricate dance of branch creations where on the command line resubmitting a patch is a trivial operation. This is frustrating and unfair for contributors to have to work with.With SourceHut, we can do this ourselves without having to ask. It's smoother on all sides.

Finally, code review is much easier as we can simply just read the patch set in our editor and reply to it inline, where as GitHub comments carve up the code into tiny blocks and small viewports.

## Web Interface

For starters, you can browse the project code at [https://sr.ht/\~mpdehaan/jetporch/](https://sr.ht/\~mpdehaan/jetporch/)

## Checking Out The Code

To work on the code you do not need a SSH key or a password. An anonymous public clone is all you need!&#x20;

```
$ git clone https://git.sr.ht/~mpdehaan/jetporch
```

## Want Online Backups?

If you would still like to put your own copy of the source code online (on GitHub, GitLab, GitBucket, etc) to share a fork with someone you can do this, but it is not required . Most people will not need to do this, but it's fine if you do want to.

```
git remote add my-repo my-url-here

git push my-repo
```

## Sending In A Patch

Working on the code and sending in a patch is easier than GitHub after you get the hang of your first patch submission.

First, you need to configure your git checkout of Jet according to the instructions on [https://git-send-email.io](https://git-send-email.io).  Don't skip these steps as attaching a patch in email will not work - this will take about 3 minutes and you'll only have to do it once.  Many SaaS email services will require setting up an application password to use. The online instructions are fairly brilliant and include instructions for setting up mail clients like Gmail, Fastmail, Proton, and so on.

To start work, make sure you are up to date with the upstream code:

```
git checkout main

git pull --rebase
```

Now, start a temporary throwaway "topic branch" for the feature you want to add:

```
git checkout -b memorable_description
```

Do your work. &#x20;

```
# edit some files
git commit -a

# more edits
git commit -a
```

If your patch is going for some time and you want to pull in upstream code changes to work along with it, you can always run:

```
git pull --rebase

# OR ...

git config pull.rebase true

git pull
```

If you always rebase, this keeps your patches on the top of the stack, which will be important in the next step.&#x20;

Once you have made sure your patch works, is well tested, and free from warnings, you are ready to think about submitting it.&#x20;

You should make sure your patch looks correct before emailing it. Ensure sure it includes only changes related to the one idea you are working on, and does not contain work from previous efforts.

Use "git log | less" to see how many commits you made, and if it is just one commit, the command you wish to run next is:

```
git format-patch HEAD^
```

Add two carets if there are two commits, etc.

Take a look at the patch and see if it contains what you want.

Once you decide your patch looks good, you are ready to send it. You cannot use your mail client to do this, and instead will run git-send-email using the exact same pattern as above. Copy this command (except maybe for the number of carets).

```
git config sendemail.to "~mpdehaan/jetporch-devel@lists.sr.ht"

git send-email HEAD^ 
```

This will open your editor (such as vim, emacs, etc) and allow you to add a description describing the patch set.  Make sure to explain what the purpose of the patch is and any relevant info.

If there are any questions or comments, [Michael](https://home.laserllama.net) will reply to the patch. [Status of the patch is also shown in the web interface](https://lists.sr.ht/\~mpdehaan/jetporch-devel).  If your patch needs changes, the process is to submit a new patch, which can simply include additional commits on the same topic branch.&#x20;

```
git send-email --annotate -v2 HEAD^^
```

The number of carets is how many commits to include in the patch.  If you want to get fancy, you could use git commit -a --amend to update the last changeset instead.

Now you can switch back to the main development branch, submit other patches that don't depend on the outstanding patches, and so on.  It's easy to have multiple patches and features working at the same time this way.

## About Patch Discussion

The patch submission list is a review list, not a full discussion list about future possible ideas or how internals work.  As such, replies to patches should be about the patch in question, and all topics send to the patch list should contain a patch.

Discussion not about code review should happen on [Discord](discord-chat.md). Be sure to join the development channels!

Discussion of features ahead of submitting patches is strongly recommended, this is not required for bugfixes, though it can still be helpful depending on complexity. The more discussion happens, the less code review and modifications need to happen, so in general, up front discussion is _great_.&#x20;

Thank you and we look forward to patches from you!  If you hit any problems or have git questions of any kind, feel free to ask in chat, we want everyone to have a good time with this process!

