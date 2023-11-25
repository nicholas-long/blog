+++
title = 'Create Python Script From Burpsuite Saved Request'
date = 2023-11-25T15:47:03-06:00
draft = false
+++

# Motivation for the Project
In web penetration testing, it is commonplace to write quick and dirty python scripts to perform a web requests.
Burpsuite has a feature to copy a request as a valid curl command that you can run to recreate an identical HTTP request.
This command is a valid bash command that could then be used to create a bash script to perform the same request.
What if there was also a way to automatically generate a boilerplate python requests script that sets all appropriate headers, cookies, and post content?

- The [full script is located here](https://github.com/nicholas-long/environment/blob/main/zet/20230928133216/README.md)

# Code
## Decoding BurpSuite Saved Request Format

Data is stored in burpsuite request in XML format.
The full HTTP request data is base64 encoded within the `reqeust` tag.
We can use the `xpup` go library/CLI tool to parse the XML.

- get raw HTTP request data from burp saved request - code from `get-burp-http-saved-request.sh`
```bash
cat $1 | go run github.com/ericchiang/xpup@latest //request | base64 -d
```

## Getting HTTP Content
The HTTP request has headers and optional content, if it is a POST request.
The headers all occur before the first blank line.
In order to retrieve the content, we can use a simple AWK script `get-http-post-content.awk`
```awk
BEGIN { content=0 }
content { print }
/^$/ { content=1 }
```

## Decoding URL Encoded POST Params
A simple python script can decode the HTML post parameters.
HTTP post parameters are separated by ampersand and equal characters.
The `unquote` function from `urllib` can help unescaping strings.
`repr` returns a printable version of the string.

```python
from urllib.parse import unquote
params = {}
s = input()
for p in s.split('&'):
    elems = p.split('=')
    k = elems[0]
    v = elems[1]
    params[k] = unquote(v)
print(repr(params))
```

## Decoding Cookies
similar to the process of decoding URL post parameters, for decoding cookies, a python script can split the cookie header from the client
```python
from urllib.parse import unquote
params = {}
import fileinput
for rawline in fileinput.input():
    line = rawline.rstrip("\n")
    elems = line.split('=')
    k = elems[0]
    v = elems[1]
    params[k] = unquote(v)
print(repr(params))
```

## Script to Generate Python Code
This is the full script to generate the HTTP requests.
It has been documeneted and comments have been added to help clear up each step.

```bash

xpup="go run github.com/ericchiang/xpup@latest"
file="$1"
http_request=$(mktemp)

# parse burpsuite saved request file and convert to python requests script for copying
cat $file | $xpup '//request' | base64 -d > $http_request
# get rid of line feeds in HTTP headers
dos2unix $http_request 2>/dev/null

# get url from request file
url=$(cat $file | $xpup //url)
# get content type to check later
ct=$(awk '/^Content-Type/ { print $2 }' $http_request)
# the request method is the first word of the first line in the request headers
method=$(awk 'NR == 1 { print $1 }' $http_request)
# get HTTP get parameters
getparams=$(echo $url | $ENVIRON_BASEPATH/zet/20230928133216/get-url-params.py)
# get http headers and skip reserved headers that are not relevant
# content length should be handled automatically by requests library
http_headers=$(awk '
  BEGIN {
    FS = ": "
    OFS = "\t"
  }
  /^Cookie/ { next }
  /^Host/ { next }
  /^Content-Length/ { next }
  /^Connection/ { next }
  /^Upgrade-Insecure-Requests/ { next }
  /^Accept/ { next }
  /^Content-Type/ { next }
  NF == 2 { $1 = $1; print }
  /^$/ { exit 0 }
  ' $http_request | $ENVIRON_BASEPATH/zet/20230928133216/tsv-to-python-dict.py)

cookies=$(awk '
/^Cookie/ {
  gsub(/;/,"")
  for (n = 2; n <= NF; n++)
    print $n
}
' $http_request | $ENVIRON_BASEPATH/zet/20230928133216/decode-cookies.py)

#--------------------------------------------------------------------------------
# begin writing output script here

# python headers of script here
cat << HEADER
import requests

url = "$url"
cookies = $cookies

getparams = $getparams
headers = $http_headers

HEADER

if [ $method == "POST" ]; then
  if [ $ct == "application/x-www-form-urlencoded" ]; then
    postdata=$( $ENVIRON_BASEPATH/zet/20230928133216/get-http-post-content.awk $http_request | $ENVIRON_BASEPATH/zet/20230928133216/decode-post-params.py)
    cat << PYTHON
postdata = $postdata
r = requests.post(url, data=postdata, headers=headers, cookies=cookies)
PYTHON
  elif [ $ct == "application/json" ]; then
    postdata=$( $ENVIRON_BASEPATH/zet/20230928133216/get-http-post-content.awk $http_request )
    cat << PYTHON
postjson = $postdata
r = requests.post(url, json=postjson, headers=headers, cookies=cookies)
PYTHON
  fi
elif [ $method == "GET" ]; then
    cat << PYTHON
r = requests.get(url, params=getparams, headers=headers, cookies=cookies)
PYTHON
fi

# footer of script
cat << FOOTER
print(r.text)
FOOTER

rm $http_request
```
