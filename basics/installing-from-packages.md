# 💾 Installing From Packages

{% hint style="info" %}
**Installing From Source Is Encouraged**

As Jetp just made it's very first release (Tech Preview 1 on September 29th, 2024), you may want to consider following the development branch and [Installing From Source](installing-from-source.md) - releases are expected to occur approximately monthly, so you may be missing out on features and bug fixes that already exist on the development branch.  For release information see [Roadmap](../community/roadmap.md), [Status](../community/status.md), and the [Blog](../community/blog-and-announcements.md).

We always want to make the development branch very stable -- so there's no problem with following along with the development branch if you like.  If you are not following along with [chat](../community/discord-chat.md), and even if you are, many new development features will be announced on the [Blog](../community/blog-and-announcements.md) as they are added.
{% endhint %}

## Rust Packages

Once Rust is installed (see instructions under [Installing From Source](installing-from-source.md)), the latest jetp release can be installed with:

```
$ cargo install jetp
```

this will install the jetp executable into \~/.cargo/bin. &#x20;

```
$ which jetp
$ jetp --version
```

### Native OS Packages

For the current release, official OS packages (RPMs, debs, etc) endorsed by JetPorch are not yet available.  These may be available in the future.

