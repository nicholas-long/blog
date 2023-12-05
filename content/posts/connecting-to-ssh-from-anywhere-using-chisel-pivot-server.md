+++
title = 'Connecting to SSH From Anywhere Using Chisel Pivot Server'
date = 2023-12-05T12:03:09-06:00
draft = false
+++

I recently undertook a project to get conected to my devices from my phone.
My goal is to be able to SSH from aywhere.
I'm currently writig this in vim from a terminal on my phone connected to a laptop across the room over the internet without forwarding ports on my router.

I have a [Samsers Foldable Bluetooth Keyboard](https://www.amazon.com/dp/B07KNHLHDW?ref=ppx_pop_mob_ap_share) that I bought from amazon.
It allows me to type on the phone like a terminal, but it folds up into roughly the size of a phone itself so I can carry it around.
It connects to the phone instantly with bluetooth when you unfold it to open it. It feels very slick.
The one complaint I have with this keyboard is that it does not have a real escape key.
You have to type escape awkwardly with the function keys.

I am using the webssh app for a termial.
It is pretty good, but I use control-space for my tmux hotkey, and this terminal does not support that.
It does not seem to support the nerd font glypths in tmux by default, but the missing character that appears is not ugly.
Therefore, I can use [my same environment]({{< ref "my-terminal-environment-and-dotfiles.md" >}}) on my phone, with a couple small tweaks.

- WebSSH tool for iPhone: https://apps.apple.com/us/app/webssh-sysadmin-tools/id497714887

I am using chisel as a server for routing the ports.
It supports reverse port forwards, and it uses network bandwidth optimally, so it is perfect for this job.
It is commonly used for port forwarding in penetration testing.
It is possible to forward ports or even set up a socks server on a target machine with one simple command.

- chisel tool for pivoting https://github.com/jpillora/chisel

Chisel is written in go, so it works on any platform, and it can be installed with one simple command:
```bash
go install github.com/jpillora/chisel@latest
```

I needed a [script for the clients to reconnect](https://github.com/nicholas-long/environment/blob/main/zet/20231205173345/README.md) with chisel periodically if they are disconnected.
This is also an opportunity to include some configuration specific to each computer.
```bash
#!/bin/bash

configfile="$HOME/.chiselsetup"

while [ 1 ]; do
  # need variables creds and port
  source "$configfile"
  chisel client --auth "$creds" nicklong.xyz:9000 "R:$port:127.0.0.1:22"
done
```

This script loads a config file from the user's home directory to get the credentials and port to forward.
An example of the configuration file used by this script is included below:
```bash
creds="username:SUPER_SECRET_PASSWORD"
port=9001
```

# Setting up the server
```bash
nohup chisel server --port 9000 --reverse --auth username:SUPER_SECRET_PASSWORD
```
This command starts the chisel server on port 9000.
The username and password are provided as command line arguments or can optionally be provided as a file.
The `nohup` command ensures that this command continues running after the current terminal session is closed.
I also put this command in a crontab to run on `@reboot`.

- DevSecOps tip: an authentication file should be used with chisel to avoid leaking passwords on the command line.
