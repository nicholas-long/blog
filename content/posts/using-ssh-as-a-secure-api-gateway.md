+++
title = 'Using SSH as a Secure API Gateway'
date = 2023-12-12T09:27:32-06:00
draft = false
+++

Recently I discovered that I can create an iOS shortcut step to connect to an SSH server and run a script.
This is similar to how the SSH client can run a program when you specify a command as an argument to SSH when connecting.

![image of SSH step in shortcuts app](/run-shortcut-ssh-step.png)

Doing this, I can create a script that reads standard input as input to the program, which is analogous to the same way as a CGI script can handle POST data.
I can use JSON data in order to avoid issues with parsing, but if the program is simple enough, then lines of text will even suffice.

# Pros and Cons
The advantages of using SSH keys as an authentication mechanism include the ability to set explicit access controls for endpoints.
This determines who should be able to access them by providing them with a key. 
However, a disadvantage of this method is the speed of the connection handshake.
If the API endpoint will be called often, or needs to respond within milliseconds, this will be too slow.

# Secure API Endpoints for Siri Shortcuts
Siri shortcuts are a way to make integrations and small apps on your phone very quickly.
For example, if I wanted to scan a list of books and store the list on my server, I can make a shortcut step to scan barcodes. Then, I could make a CGI script to just log standard input on a linux server and call it from within a shortcut, passing in the barcode that was read.
However, doing this will expose a CGI script to the internet. Now it needs additional authentication or maybe a secret token.
With an SSH script, the authentication is already taken care of, and nobody can access the endpoint without the right private key.
