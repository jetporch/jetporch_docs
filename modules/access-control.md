# 🔓 Access Control

Access control modules are concerned with creating users and groups.  There won't be too many of these.



{% hint style="info" %}
**Where's OS X?**

Because OS X user management is very very different, and also mostly because developer setup rarely needs them, we do not attempt to provide OS X user/group management for the access control modules. &#x20;
{% endhint %}



### Access Control Module Index

| Module                           | Description          |
| -------------------------------- | -------------------- |
| [group](access-control.md#group) | Manages Linux groups |
| [user](access-control.md#user)   | Manages Linux users) |

### group

adds, removes, and manages user groups



|        |                                                                                                                                      |
| ------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| group  | Required. name of the group                                                                                                          |
| gid    | Numeric GID to use when creating the group.                                                                                          |
| users  | A list of users to add to the group.                                                                                                 |
| append | Boolean, default false. If true, the users in the group parameter list will be added to the group instead of made to match the list. |
| system | Boolean, default false. Creates the group as a system group.                                                                         |
| remove | Boolean, default false. If true, the group will be removed instead of created or modified.                                           |

### user

The user module adds, removes, and manages user accounts.





| Parameter           | Details                                                                                                                                                                                                             |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| user                | Required. the name of the user account to manage                                                                                                                                                                    |
| uid                 | Numeric user ID to assign to the account. Used on creation only.                                                                                                                                                    |
| gid                 | Primary group name for the account.                                                                                                                                                                                 |
| groups              | List of groups the account should be a member of                                                                                                                                                                    |
| append              | Boolean, default false - modifies the way existing  users are treated. If supplied, any missing groups listed in 'groups' will be added to the list of groups for the user, but missing groups will not be removed. |
| create\_home        | Boolean, default true - when false, a home directory will not be created for the user account.  Applies to creation only.                                                                                           |
| create\_user\_group | Boolean, default true - unless false, a group with the same name as the user will be created automatically.                                                                                                         |
| gecos               | sets/updates the [gecos field](https://en.wikipedia.org/wiki/Gecos\_field)                                                                                                                                          |
| shell               | Default shell to assign to the user                                                                                                                                                                                 |
| system              | Boolean, default false. If true, creates the user as a system account.                                                                                                                                              |
| remove              | Boolean, default false.  If true, will remove the account specified by 'user' if it exists, and other parameters will be ignored. Modified by 'cleanup'.                                                            |
| cleanup             | Boolean, default false. If true, when removing user accounts the user's home directory and mail will also be removed.                                                                                               |



```
tasks:

   - !user
     user: bob
     
   - !user
     user: alice
     remove: true
     cleanup: true
     
   - !user
     user: frodo
     groups:
       - hobbits
       - shire
     uid: 5000
     shell: /bin/sh
     append: true
       
```



{% hint style="info" %}
**Looking For More Features?**

Additional features to the user module may make good items for future patches and contributions.  These may include adding password setting capabilities, etc.
{% endhint %}



##
