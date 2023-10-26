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
