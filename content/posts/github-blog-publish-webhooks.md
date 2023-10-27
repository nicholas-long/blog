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

## adding security
At first I tried using the HMAC digest sent from github.
In order to properly validate the message, I have to checksum the payload and signature with HMAC SHA-256.
That is difficult. I would need to write a python script in order to hash the input file with HMAC.
I think that having such a difficult verification method for webhooks can be a negative.
Like many developers do, it is tempting to just skip all security and authentication concerns while the project is in development until "later when things are working".

The more expedient method which still provides protection from random people calling the endpoint is to add a secret HTTP GET query parameter.
Webhooks are not visible publicly on github, and the endpoint is HTTPs and protected by a valid SSL certificate. Eavesdropping and intercepting the secret should not be a concern.
All HTTP GET parameters go into an environment variable `QUERY_STRING` in the CGI standard.
```bash
$ cat blog-webhook.cgi
#!/bin/bash

echo ""
echo "updating blog..."
if echo "$QUERY_STRING" | grep "PUT_YOUR_HTTP_GET_SECRET_HERE"; then
  nc localhost 8081
else
  echo "You didn't say the magic word!"
fi
```
- how to validate webhooks on github https://docs.github.com/en/webhooks/using-webhooks/validating-webhook-deliveries#examples

# enabling webhooks on github
- go to project settings on github - the upper right tab
- create a new webhook
- add a payload and check for it for security when CGI script gets called.
![github webhook interface screenshot](/github-webhooks-screenshot.png)
