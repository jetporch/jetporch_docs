# 📒 Logging

Starting with the Tech Preview 2 release, jetp emits structured JSON logs to /var/log/jetp/jetp.log or, alterately, a path specified by the environment variable JET\_LOG.  These logs are intended for consumption by software, not humans, with one JSON entry per line.  Different event times will have different levels of detail.

If the path is not writeable, jet will proceed without error.

Log rotation with logrotate is strongly recommended as these logs can be particularly large especially when configuring a large number of hosts, processing complex configurations, or running very many builds that all rely on jetp.

From here, logfiles can be sent to various log aggregrators such as Splunk, Elastic Search, and so on.

The logs are largely self explanatory and include information on playbook paths and task results, as well as summaries of playbook statuses similar to what is produced by the Jet command line.





<img src="../.gitbook/assets/file.excalidraw (14).svg" alt="" class="gitbook-drawing">

### JSON Event Types



|                       |                                                                                                                                                                   | Host Specific? |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- |
| PLAYBOOK\_START       | Playbook began executing                                                                                                                                          | No             |
| PLAY\_START           | A particular play in a playbook began executing                                                                                                                   | No             |
| ROLE\_START           | A particular role began executing                                                                                                                                 | No             |
| TASK\_STATUS          | Status of a particular task on a particular host that is not error related. TASK\_CHECK\_STATUS is used instead in check mode, or TASK\_FAILED for failed events. | Yes            |
| TASK\_CHECK\_STATUS   | Same as TASK\_STATUS but is used in check mode operations.                                                                                                        | Yes            |
| TASK\_FAILED          | Same data as TASK\_STATUS but is used when modules fail on particular hosts and the errors are not ignored                                                        | Yes            |
| HOST\_CONNECT\_FAILED | An event that is registered when it is impossible to connect or login to a particular host.                                                                       | Yes            |
| SUMMARY               | A detailed report of statistics from a playbook run. Also signals the playbook is done executing.                                                                 | No             |





### JSON Log Event Fields



Not all event types will have all fields in the JSON entries.  Consulting the log examples from a live run is recommended for understanding values.



| Json Field     | Description                                                                                                                                                                                                                                                     |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| event          | the event type string. different event types will have different JSON data members.                                                                                                                                                                             |
| run            | a GUID that can be used to associate all events related to a particular playbook run                                                                                                                                                                            |
| start          | the time the playbook run was initiated in rfc 2822 format (UTC)                                                                                                                                                                                                |
| now            | the current time in rfc 2822 format (UTC)                                                                                                                                                                                                                       |
| elapsed        | the elapsed time in seconds into the playbook run, for PLAYBOOK\_COMPLETE events this indicates the complete duration of the run                                                                                                                                |
| playbook       | the "name" field of the playbook being run                                                                                                                                                                                                                      |
| playbook\_path | the full path to the playbook being run                                                                                                                                                                                                                         |
| role           | the current role name, if any                                                                                                                                                                                                                                   |
| task           | the name of the current task.  If no name was provided, the name of the module is used                                                                                                                                                                          |
| task\_ct       | Numeric. The number of tasks "deep" the task is into the playbook run.  This is not reset between roles                                                                                                                                                         |
| cmd            | the name of the cmd being run, which is provided for shell module task events and any failures involving cmd statuses.  Intermediate command statuses for successful commands during normal module execution are not logged.                                    |
| cmd\_rc        | The return code for any command from a shell module, or from a failed module execution                                                                                                                                                                          |
| cmd\_out       | The output text from a shell module call or a failed module execution                                                                                                                                                                                           |
| task\_status   | The status for TASK\_STATUS events.  TASK\_FAILED events are seperate.  These are shown in another table.                                                                                                                                                       |
| host           | For host specific actions, this is the name of the host in inventory related to the current event.  Some events are global and do not have a host entry.                                                                                                        |
| summary        | a JSON map of numeric information.  This gives a detailed statistics report on the playbook run, including totals for various types of host states and how many modifications were performed, similar to the table at the end of the human-readable jet output. |







