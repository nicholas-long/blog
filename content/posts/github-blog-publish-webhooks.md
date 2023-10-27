+++
title = 'Setting Up Github Webhooks for the Blog'
draft = false
+++

```bash
$ cat blog-webhook.cgi
#!/bin/bash

echo ""
/opt/update-blog

$ ls -al update-blog
-rwsr-sr-x 1 ubuntu ubuntu 15976 Oct 26 21:56 update-blog
```

- code for update-blog setuid executable
```c
#include <stdio.h>
#include <stdlib.h> // for getenv

// gcc setuid-blog-update.c -o update-blog

int main() {
  system("cd /home/ubuntu/blog && ./pull-publish");
  return 0;
}
```

- this quick and dirty solution is currently not working

The setuid script does not have access to run `cd` to get into the directory. I am not sure why.
Hugo does not seem to build when run under the setuid script under www-data.

- trying adding git config setting
```bash
git config --global --add safe.directory /opt/blog
```
