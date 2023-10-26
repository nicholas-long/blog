+++
title = 'Setting Up Server'
date = 2023-10-26T14:36:46-05:00
draft = false
+++

I am setting up crons on the server.
One minute was fine for testing, but I think that is too frequent for actual use.

- testing with one minute
```bash
crontab -l
* * * * * cd ~/blog && ./pull-publish
```

- five minutes
```bash
crontab -l
*/5 * * * * cd ~/blog && ./pull-publish
```
