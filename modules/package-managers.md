# 🎁 Package Managers

The package management modules wrap package manager commands for easier, more declarative use. &#x20;

### Package Manager Module Index



| Module                                   | Description                                                                                                                   |
| ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| [apt](package-managers.md#apt)           | Package management for Debian and derivatives like Ubuntu                                                                     |
| [dnf](package-managers.md#dnf)           | Package management for current "Enterprise Linux" OSes and Fedora                                                             |
| [homebrew](package-managers.md#homebrew) | Package management for OS X                                                                                                   |
| [pacman](package-managers.md#pacman)     | Package management for Arch Linux                                                                                             |
| [yum](package-managers.md#yum)           | Package management for "Enterprise Linux" 7  and before. (Actually an alias, this is the same underlying Jet module as 'dnf') |
| [zypper](package-managers.md#zypper)     | Package Management for SuSE distributions                                                                                     |

### apt

Installs packages on Debian and Ubuntu systems with the apt package manager. &#x20;

{% hint style="warning" %}
**Apt-Cache Information**

the apt-module does not run "apt update" for you, if this is important (and it should be), use the shell module to do it prior to running apt module tasks.
{% endhint %}

```
tasks:

   - !apt
     package: redis
     
   - !apt
     package: redis
     version: 5:6.0.16-1ubuntu1
     
   - !apt
     package: redis
     update: true
     
   - !apt
     package: redis
     remove: true
```



| Parameter   | Description                                                                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **package** | Required. Name of the package to install or remove                                                                                                                    |
| **version** | Optional version of the package to install or upgrade to.  Must be matched exactly.                                                                                   |
| **update**  | Boolean, default false. If true, will attempt to update an already installed package to the latest version in the repo or the given version, if version is specified. |
| **remove**  | Boolean, default false. If true, will remove the package if installed rather than attempting installation if not installed.                                           |

### dnf

Installs packages for "Enterprise Linux" flavor operating systems using the dnf package manager.  Specific versions can be specified, or not.

"remove" will remove the package if currently installed.

<pre><code><strong>tasks:
</strong><strong>
</strong>   - !dnf
     package: tree
     
   - !dnf
     package: tree
     version: 1.8.0
     
   - !dnf
     package: tree
     update: true
     
   - !dnf
     package: tree
     remove: true
</code></pre>

| Parameter   | Description                                                                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **package** | Required. Name of the package to install or remove                                                                                                                    |
| **version** | Optional version of the package to install or upgrade to.  Must be matched exactly.                                                                                   |
| **update**  | Boolean, default false. If true, will attempt to update an already installed package to the latest version in the repo or the given version, if version is specified. |
| **remove**  | Boolean, default false. If true, will remove the package if installed rather than attempting installation if not installed.                                           |

### homebrew

Installs Mac packages with homebrew.  &#x20;

<pre><code><strong>tasks:
</strong><strong>
</strong>   
   - !homebrew
     package: tree
     
   - !homebrew
     package: tree
     remove: true
</code></pre>

<table><thead><tr><th width="222">Parameter</th><th>Description</th></tr></thead><tbody><tr><td><strong>package</strong></td><td>Required. Name of the package to install or remove</td></tr><tr><td><strong>version</strong></td><td>Optional version of the package to install or upgrade to.  Must be matched exactly.</td></tr><tr><td><strong>update</strong></td><td>Boolean, default false. If true, will attempt to update an already installed package to the latest version in the repo or the given version, if version is specified.</td></tr><tr><td><strong>remove</strong></td><td>Boolean, default false. If true, will remove the package if installed rather than attempting installation if not installed.</td></tr></tbody></table>

### pacman

Installs packages for Arch Linux operating systems using the pacman package manager.  With pacman, specifying specific versions and using 'update' may potentially break system packages because of Arch's rolling release system.  Consider running "pacman -Syu" for updates.

<pre><code><strong>tasks:
</strong><strong>
</strong>   
   - !pacman
     package: extra/tree
     
   - !pacman
     package: extra/tree
     remove: true
</code></pre>

<table><thead><tr><th width="222">Parameter</th><th>Description</th></tr></thead><tbody><tr><td><strong>package</strong></td><td>Required. Name of the package to install or remove</td></tr><tr><td><strong>version</strong></td><td>Optional version of the package to install or upgrade to.  Must be matched exactly.</td></tr><tr><td><strong>update</strong></td><td>Boolean, default false. If true, will attempt to update an already installed package to the latest version in the repo or the given version, if version is specified.</td></tr><tr><td><strong>remove</strong></td><td>Boolean, default false. If true, will remove the package if installed rather than attempting installation if not installed.</td></tr></tbody></table>

### yum

At some point at time "Enterprise Linux" distros and Fedora changed their package manager while keeping the commands and interface the same. Since they mostly work the same, internally in Jet, we can run mostly the same code to support both modules. Internally the Jet module will see if the /usr/bin/yum or /usr/bin/dnf command is available and run the correct command as appropriate. This means the module may be referenced as "!dnf" or "!yum" in Jet playbooks and it makes no difference, it will run the correct command either way.

There is no need to write playbook logic to figure out whether to use !dnf or !yum when configuring a server, nor to use the facts module prior to  using either module. Take care though - various configuration files for the package manager still do have different locations.

```
tasks:

    - !yum
      package: tree
      
    # .. and so on, see examples for the dnf module
    
```



| Parameters                         | Description                        |
| ---------------------------------- | ---------------------------------- |
| See [dnf](package-managers.md#dnf) | See [dnf](package-managers.md#dnf) |

### zypper

Installs packages on SuSE using the zypper package manager. &#x20;

```
tasks:

   - !zypper
     package: tree
     
   # arguments work like all the other packaging modules
     
```



| Parameter   | Description                                                                                                                                                           |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **package** | Required. Name of the package to install or remove                                                                                                                    |
| **version** | Optional version of the package to install or upgrade to.  Must be matched exactly.                                                                                   |
| **update**  | Boolean, default false. If true, will attempt to update an already installed package to the latest version in the repo or the given version, if version is specified. |
| **remove**  | Boolean, default false. If true, will remove the package if installed rather than attempting installation if not installed.                                           |

###

