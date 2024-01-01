# 🕒 Services

Service modules manage long-running operations.  OS services for all Linux/Unix platforms are definitely a good fit for this category, but other service management systems (such as supervisord) could also be relevant for future module additions.

### Service Module Index

| Module                                 | Description                                                         |
| -------------------------------------- | ------------------------------------------------------------------- |
| [sd\_service](services.md#sd\_service) | systemd-based service management for nearly all Linux distributions |

### sd\_service

Manages services for operating systems that use systemd.



```
tasks:
   - !sd_service
     service: redis
     started: true
     enabled: true
     
   - !sd_service
     service: nginx
     started: false
     enabled: false
     
   - !sd_service
     service: httpd
     enabled: true
     restart: true
     
```

"enabled", "started", and "restart" are all optional parameters.

The restart flag will be mostly used with handlers to restart services when configuration files change, this is described in the [Play](../playbooks/plays.md) documentation.



| Parameter   | Description                                                                                                            |
| ----------- | ---------------------------------------------------------------------------------------------------------------------- |
| **service** | **Required**. The name of the service to manage                                                                        |
| **started** | Boolean. Whether the service should be started or not.  Omission implies this property is not managed.                 |
| **enabled** | Boolean. Whether the servcie should be enabled to start on boot or not.  Omission implies this property is not managed |



