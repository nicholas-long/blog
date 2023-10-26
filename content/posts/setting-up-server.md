+++
title = 'Setting Up Server Devops'
date = 2023-10-26T14:36:46-05:00
draft = false
+++

I am setting up crons on the server.

- testing with one minute
```bash
crontab -l
* * * * * cd ~/blog && ./pull-publish
```
