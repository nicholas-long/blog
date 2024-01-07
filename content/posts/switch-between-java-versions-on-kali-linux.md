+++
title = 'How to Switch Between Java Versions on Linux Without Breaking Things'
date = 2024-01-07T11:42:45-06:00
draft = false
+++

Sometimes, when working with Java payloads, it might be necessary to switch to a different Java version or use specific version in order to run a program.
One of the suggestions available when researching this was to [use alternatives to switch versions](https://askubuntu.com/questions/740757/switch-between-multiple-java-versions), but this seems like a relatively permanent solution to a temporary problem.

Java versions installed on debian operating systems are stored in `/usr/lib/jvm`.
In there are nested `bin` directories containing java.
Java also requires a home directory set in the `JAVA_HOME` directory for java JDK reasons.

Recently I needed to switch from OpenJDK version 17 to 11 in order to get a [ysoserial](https://github.com/frohoff/ysoserial) payload to generate.
- switching to java 11 on Kali
```bash
export PATH=/usr/lib/jvm/java-11-openjdk-arm64/bin:$PATH
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-arm64
```

# Developing a Version Selector Script
Since I like automating everything, I decided to come up with a [Java version selector script](https://github.com/nicholas-long/environment/blob/main/zet/20240107171624/README.md) in order to do this on the spot when I need to.
since the script is setting environment variables, I need to actually source it in the easiest way to do this is to define a bash alias in my `bashrc`.
My bashrc is available on github in my [environment scripts]({{< ref "my-terminal-environment-and-dotfiles.md" >}})

```bash
alias java-selector='source $ENVIRON_BASEPATH/zet/20240107171624/java-selector'
```

I am using the fzf program to create an interactive menu to and return the path to the selected java binary.
A simple awk script to cut the directory path on slashes and remove the last one helps find the relevant paths.
```bash
#!/bin/bash

javapath=$(find /usr/lib/jvm -name java | fzf | awk 'BEGIN {OFS=FS="/"} {NF--; print}')
javadir=$(echo "$javapath" | awk 'BEGIN {OFS=FS="/"} {NF--; print}')
export PATH=$javapath:$PATH
export JAVA_HOME=$javadir
```

- example of switching java versions
```
┌──(parallels㉿kali-gnu-linux-2023)-[~/environment/zet/20240107171624]
└─$ java-selector

┌──(parallels㉿kali-gnu-linux-2023)-[~/environment/zet/20240107171624]
└─$ java --version
Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
openjdk 11.0.20-ea 2023-07-18
OpenJDK Runtime Environment (build 11.0.20-ea+7-post-Debian-1)
OpenJDK 64-Bit Server VM (build 11.0.20-ea+7-post-Debian-1, mixed mode)

┌──(parallels㉿kali-gnu-linux-2023)-[~/environment/zet/20240107171624]
└─$ java-selector

┌──(parallels㉿kali-gnu-linux-2023)-[~/environment/zet/20240107171624]
└─$ java --version
Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
openjdk 17.0.9 2023-10-17
OpenJDK Runtime Environment (build 17.0.9+9-Debian-2)
OpenJDK 64-Bit Server VM (build 17.0.9+9-Debian-2, mixed mode, sharing)
```
