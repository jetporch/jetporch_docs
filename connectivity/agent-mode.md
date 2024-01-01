# 🌎 Agent Mode

**Coming Soon**

Ordinarily jet works in a SSH push-based mode.  This is a great fit for configuring entire service tiers.  Running jet with 100 parallel threads is definitely a normal thing to do, though if configuring, say, 100k machines all at once, this no longer becomes an effective strategy.

For this, jet will be adding a command called 'jetp watch', which may work as follows:

```
jetp watch --watch-config watch.yml
```

This will start a simple agent process.

To understand the watch command, it's easiest to talk about the configuration file and how it effects operation:

```
check_command: /var/jetp/check.sh
download_command: /var/jetp/download.sh {{ download_path }}
jet_command: jetp local --playbook {{ download_path }}/pb.yml 
check_interval: 200
splay: 100
on_ok: /var/jetp/ok.sh {{ play_uuid }}
on_fail: /var/jetp/fail.sh {{ play_uuid }}
```

By changing the various commands, we can decide when to run jet on a remote system using a variety of criteria, and where to get content from.

For deciding when to kick off a playbook run, we set this with **check\_command** - these may include looking for watch files or checking a message bus (Kafka, NATS, RabbitMQ, etc), bucket, or REST API.  The script can return 0 when jet needs to run, and 1 when it does not need to run.

The **download\_command** can pull content from NFS, or a bucket like s3 or Minio, and would be triggered when the check command indicates a run is desired.  A random GUID location is created with "\{{ download path \}}" for optional use.  This same parameter is made available to the jet command later.  If the content is already on the filesystem, this parameter could be ignored.

The system can wait **check\_interval** seconds between running the check command.

**splay** waits a random number of seconds (less than this value) on startup to minimize thundering herd scenarios.

**on\_ok** and **on\_fail** can optionally run certain commands when failures occur.  This might be to signal a REST service or upload logfiles.&#x20;

This feature will also be heavily useful when used with pending structured logfile support, making it integrate well with logfile aggregration services like Splunk, Logstash, so on.

All of these options can be put together in various ways.  For instance, to just run a playbook periodically, the check\_command can be /usr/bin/true.  If content is on NFS, the download command could also be _/usr/bin/true_.



