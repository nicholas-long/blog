+++
title = 'Setting Up Github Webhooks for the Blog'
date = 2023-10-27T10:36:00-05:00
draft = false
+++

# testing setuid
I tried making a setuid executable to change to the user and run update scripts, but it is was not working.
I think the `PATH` and other environment variables are not configured right when running under `www-data` and switching with a setuid executable.
Therefore, the simplest solution is to create a local service to run the update command.

# creating a local service
- code for a simple local "service" to perform the update
  - literally any tcp connection to this port on localhost will launch the update, so make sure it is only open to localhost.
  - need to make sure socat is installed as a dependency on the server - it doesn't come with ubuntu by default
```bash
$ cat webhook-local-worker
#!/bin/bash

socat TCP4-LISTEN:8081,fork,bind=127.0.0.1 exec:./pull-publish
```

- webhook simple CGI script to call local service
```bash
$ cat blog-webhook.cgi
#!/bin/bash

echo ""
echo "updating blog..."
nc localhost 8081
```

  - installing new crontab for local service
  ```crontab
  @reboot cd ~/blog && ./webhook-local-worker
  ```

# enabling webhooks on github
- go to project settings on github - the gear in the upper right
- create a new webhook
- add a payload and check for it for security when CGI script gets called.
