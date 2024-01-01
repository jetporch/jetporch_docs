# 📂 Files

File modules deal with, well, files, on the managed systems.  Many options are available for file attribute management, copying, templating, or interaction with version control.

### File Module Index



| Module                          | Description                                                                                      |
| ------------------------------- | ------------------------------------------------------------------------------------------------ |
| [copy](files.md#copy)           | copy a file verbatim, suitable for small or large files                                          |
| [directory](files.md#directory) | ensure a directory exists, manage the attributes, or remote one                                  |
| [file](files.md#file)           | ensure a file exists, manage the attributes, or remote it                                        |
| [git](files.md#git)             | check out or update a directory from source control                                              |
| [stat](files.md#stat)           | tests files/directories for existence and returns details about them.                            |
| [template](files.md#template)   | use Jet's variable systems to create a customized file, most often used with configuration files |

### Common Attributes

File modules handle transferring content to remote systems or making sure that properties of files and directories are as intended.  _Most_ file modules take some common attributes:



| Common Attribute | Description                                                                                                  |
| ---------------- | ------------------------------------------------------------------------------------------------------------ |
| **owner**        | Name of the user that should own the file or directory                                                       |
| **group**        | Name of the group that should own the file or directory                                                      |
| **mode**         | An octal mode value.  The octal prefix is required.  Ex: 0o600.  Non-octal prefixed values will be rejected. |

{% hint style="info" %}
**More Attributes To Come**

This is just a starting set, for instance, SELinux attributes will be added in the near future.  Though if you think the most useful Linux command is _setenforce 0,_ we also won't judge you.
{% endhint %}

### copy

Copies individual files.

```
tasks:
   - !copy
     src: ./path.cfg
     dest: /etc/foo/path.cfg
     attributes:
         owner: thedoctor
         group: timelords
         mode: 0o600
```

Under [SSH modes](../connectivity/ssh-mode.md), the file will be pushed to the remote over SFTP protocol with the desired remote permissions mode.

If the source path is relative, the system will look in './files' relative to the playbook being executed, or if in [Role](../playbooks/roles.md) context, it will look in ./files inside the role directory. Absolute paths are also allowed.

The _attributes_ block is optional, as are all of the attributes themselves. The copy module contains built-in checksumming and will transfer data only when required.

{% hint style="info" %}
**SFTP Behind The Scenes**

Jet file transfers for modules like 'copy' and 'template' require the SSH SFTP subsystem to be enabled, which should be on in nearly all distributions. If this is _not_ the case, be sure to enable it when provisioning your systems via SSH config. If managing pre-existing systems with this feature somehow disabled, a boostrapping playbook that uses the shell module with _sed_ or _cat_ may be helpful to enable this option.
{% endhint %}

{% hint style="danger" %}
**Bandwidth And Load**

Jet is smart enough to stream large files rather than to load them in memory, which can result in fairly fast file transfers for 'copy', but executing very many file copies with a large --threads value could still overtax a control machine.  If moving large files such as ISOs, it may be better to initiate a download from the remote machine instead.
{% endhint %}



| Parameter      | Description                                                         |
| -------------- | ------------------------------------------------------------------- |
| **src**        | **Required**. Path to the source file on the local machine          |
| **dest**       | **Required**. path to the destination file on the remote machine(s) |
| **attributes** | see above                                                           |



### directory

The directory module creates directories and enforces ownership, use of 'recurse' makes the underlying chown, chgrp, and chmod commands use "-r". Directory creation always implies "mkdir -p"

```
tasks:
   - !directory
     path: /some/path/to/some/directory
     attributes:
         owner: gloop
         group: herculoids
         mode: 0x600
     recurse: true

   - !directory
     path: /some/folder/to/delete
     remove: true
```



| Parameter      | Description                                                                                               |
| -------------- | --------------------------------------------------------------------------------------------------------- |
| **path**       | The directory path to manage on the remote machine                                                        |
| **attributes** | Structure. As shown above.                                                                                |
| **recurse**    | Whether to apply attributes recursively.                                                                  |
| **remove**     | Boolean. If true, will remove the remote directory specified by dest, if present, including all contents. |

### file

Modifies attributes of files (vs directories) and can also remove them. If path does not exist, file will create an empty file at a given location. &#x20;

```
tasks:
   - !file
     path: /tmp/path.txt
     attributes:
         owner: pinky
         group: acmelabs
         mode: 0o600
         
  - !file
    path: /tmp/path2.txt
    remove: true
```



| Parameter      | Description                                                    |
| -------------- | -------------------------------------------------------------- |
| **path**       | The path of the remote file to manage                          |
| **attributes** | Structure, as above                                            |
| **remove**     | Boolean. If true, delete the remote file at path if it exists. |

### git

clones a git repository or updates it a specific version or points it at a specific branch.

The git module works with either private repositories (git:// or ssh protocol only using SSH keys) or public repositories (http:// or https:// only).  Repo authentication by password is _intentionally_ not supported with any kind of expect-based prompt-entry feature.  While passwords inside the URLs would technically work, we would discourage that practice and recommend key based authentication.

When accessing private repositories jetp must be run with '_--forward-agent_' which acknowledges signing off on some security implications, namely that untrustworthy root users on the remote (configured) machine could use SSH identities for nefarious purposes.  This is not a concern for local (non-SSH) operational modes.

```
    # SSH example, will need --forward-agent on the jetp command line
    
    - !git
      repo: git@github.com:mpdehaan/func.git
      path: /opt/func2
      branch: master
          
    - !git
      repo: https://github.com/mpdehaan/func.git
      path: /opt/func1
      branch: devel
```

This module is featured in [one of the github examples](https://github.com/jetporch/jetporch\_examples/blob/main/playbooks/git.yml) for easy experimentation with the options below.

| Parameter        | Details                                                                                                                                                                  |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| repo             | **Required.** The URL of the repo to clone.                                                                                                                              |
| path             | **Required.** The Repo location on the filesystem. **Required**.                                                                                                         |
| branch           | The name of the branch to switch to.                                                                                                                                     |
| ssh\_options     | A YAML Mapping of options to add to the SSH command line.  BatchMode: "Yes" is automatically added.                                                                      |
| accept\_hostkeys | A boolean value that defaults to true. If false, new SSH repo hostkeys will not be automatically added to known\_hosts if never before seen and clone attempts may fail. |
| update           | A boolean value that defaults to true. If false, subsequent playbook runs will not run 'git pull' on the repo unless the branch selection has changed.                   |
| attributes       | This structure is as shown in the copy module examples                                                                                                                   |



{% hint style="info" %}
**Enterprise Linux 7 Variants Only: Is Your OS "git" New Enough?**

At the present time, this command executes git with -C, which means "Enterprise Linux" 7 (which released in 2013) targets are, in stock configuraiton, too old to use with the git module. In general, we try to build modules to support Linux operating systems for the entire life of all "supported" distributions, and longer, however, the -C option (chdir) is too useful for the way Jet operates.  If you want to use this module on Enteprise Linux 7 you can upgrade your git version, or alternatively transfer content using other means.
{% endhint %}

### stat

_Added for Jetporch 0.2_

The stat module returns information about file or directory. It does not make any changes on systems.

{% hint style="info" %}
**Clever Uses of Stat**

This module is most frequently used to skip certain steps in a playbook if a file did (or did not) exist, such as in a playbook that selectively patches all servers in a given infrastructure configuration, without replying on group information.
{% endhint %}

```
tasks:

   - !stat
     path: /etc/some_file.conf
     save: foo

  # show what details are returned by 'stat'
  
  - !debug
    vars:
    - foo

  # example of reacting to the results of 'stat'

  - !shell
    cmd: /usr/bin/baz --some-arguments
    with:
       condition: (eq foo.exists true) 
       

```



| Parameters | Description                                                       |
| ---------- | ----------------------------------------------------------------- |
| path       | **Required**. The directory or filename to run the module against |
| save       | **Required**. The variable to store the result in                 |



| Return Data | Description                                          |
| ----------- | ---------------------------------------------------- |
| owner       | Owner of the file, ex: root                          |
| group       | Group owner of the file, ex: root                    |
| mode        | The octal mode as a string, ex: 0o644                |
| exists      | Boolean.  True if a file or directory exists at path |
| is\_dir     | Boolean. True if the path is a directory.            |

### template

Evaluates a local handlebars template, substituting in [variables](../playbooks/using-variables.md), and then applies it to the system.  This is great for configuration files.  The variables section has a lot more information about template syntax.  An undefined variable in a template will always produce an error.



```
tasks:
   - !template
     src: ./path.hb
     dest: /etc/foo/path.cfg
     attributes:
         owner: macgyver
         group: sg1
         mode: 0o600
```



| Parameter      | Description                                                    |
| -------------- | -------------------------------------------------------------- |
| **src**        | Required. The source path of the template.                     |
| **dest**       | Required. Where to place the template on the remote filesystem |
| **attributes** | Structure. As described above                                  |

