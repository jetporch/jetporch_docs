# ⌚ Handlers

Handlers are special tasks that mostly work like normal tasks (that is to say, they run modules), but are not necessarily run in every playbook run on every single host.  Why?  They are triggered by conditions occuring from other modules changing something.

The most common use of handlers is to restart a service only when the configuration file for that service changes, for example, if the configuration file is replaced by a new version of a template, we may want to restart the service using that configuration file.  Many users will use handlers for no other reason than this.

To phrase this differently, handlers are run only when _notified_ by some other task.  These notifications happen only when changes occur on that triggering task.  If the triggering task found that nothing needed to be changed or executed, the handler would not need to run.



<img src="../.gitbook/assets/file.excalidraw (3) (1).svg" alt="Overflow Of Handler Logic" class="gitbook-drawing">

Handler lists can be defined in [Roles](roles.md) as well, but in a loose playbook section an example would look like so:

```
# playbook.yml

- groups: 
     - all
     
  tasks:
  
      - !template
        src: foo.conf.hb
        dest: /etc/foo/foo.conf
        and:
            notify: restart foo
            
  handlers:
  
      - !sd_service
        service: foo
        restarted: true
        with:
            subscribe: restart foo
        
```

The name of the handler must match the name of the "notify" signal string for the handler to run.

{% hint style="danger" %}
**subscribe vs name**

The names of tasks in Jet are just optional _comments_, the "subscribe" parameter is how the handlers are identified. It is also possible for multiple handlers to subscribe to the same event messages!
{% endhint %}

