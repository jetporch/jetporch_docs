# 💾 Installing From Source



<img src="../.gitbook/assets/file.excalidraw (12).svg" alt="" class="gitbook-drawing">



1. Jetp is made to run from one control machine -- no software needs to be installed on remote machines in order to use Jet other than sshd. To start, we'll install Rust, so we can compile Jet. If you have Rust already installed, our current _minimal supported rust version_ is 1.6.4. Any newer version will be fine, but versions packaged by OS vendors may be somewhat more dated than what you can get from the Rust website and we may decide to move faster than some OS distributions.

[You can install using the official instructions on rust-lang.org](https://www.rust-lang.org/tools/install).

2. &#x20;Clone the project

```
git clone https://git.sr.ht/~mpdehaan/jetporch ~/jetporch
```

3. You're going to need to install some development dependencies.  If you are running any "**Enterprise Linux"** variants or Fedora:

```
yum install -y gcc openssl openssl-devel
```

If you're on **Debian** or **Ubuntu**:

```
apt-get install make gcc libssl-dev pkg-config
```

If you are on a **Mac**, you will also need to [install homebrew](https://docs.brew.sh/Installation) if you haven't already. This in turn may require running 'xcode-select --install' _first_ to install XCode Developer tools.  Once homebrew is installed, run:

```
brew install gcc openssl
```

Jet uses openssl for it's SSH client library (ssh2) when connecting to remote hosts.

4. cd into the checkout directory.  If you just made a checkout, without running  'git switch', you'll be building the 'main' branch which contains all of the latest code in development. The development branch is intended to always be usable.

```
cd ~/jetporch
```

5. &#x20;Build the project by running the Makefile.  The first time dependencies will be downloaded and compiled, successive builds will be much faster.

```
make
```

6. Add the release directory to your path, so that you can rebuild jet without having to copy it over each time. The word 'release' here means the build is optimized, it does not refer to the build coming from a particular versioned _release_.  Release builds take longer to build but are significantly faster than 'debug' builds, possibly by 5x-10x in some parts of the program! To enjoy the full power of Jet, release builds are the way to go!

```
$ export PATH=$PATH:~/jetporch/target/release/
```

7. &#x20;Run jetp

```
$ jetp --version
```

The git branch and build time will appear in your terminal. Please include this information when asking questions in chat!

{% hint style="info" %}
**One Binary To Rule Them All**

When using jet in [SSH mode](../connectivity/ssh-mode.md), you don't need to compile jet for each of your target architectures, as no binaries are transferred to remote systems. Jet works by using shell commands on the remote hosts. Some modules will require some binaries to be preinstalled, but they never require specific programming language environments like Python, Perl, or Ruby.
{% endhint %}

### Staying Updated

Once you have an a checkout and are running off the development branch, pulling in recent changes from a branch requires just a few steps:

1. &#x20;Update git to pull in any upstream changes

```
$ git pull --rebase
```

2. &#x20; Rebuild jetp

```
$ make
```

The Rust compiler will only need to rebuild the jet sources, not all of the dependencies.

### Ready For More?

If you have any problems, [stop by Discord](../community/discord-chat.md) and we'd be happy to help you out.

What's next? Browse the rest of the documentation, take a look the [example content](example-content.md) and try [executing a playbook](command-line-usage.md).

