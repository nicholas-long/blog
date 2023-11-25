+++
title = 'Create Python Script From Burpsuite Saved Request'
date = 2023-11-25T15:47:03-06:00
draft = true
+++

# motivation for the project
often when working with web penetration testing, it becomes necessary to write a quick and dirty python script to perform a web request.
burpsuite has a feature to copy a request as a valid curl command that you can run to recreate an identical HTTP request.
what if there was also a way to automatically generate a boilerplate python requests script that sets all appropriate headers, cookies, and post content?

# decoding burpsuite saved request format format

data is stored in burpsuite request in XML format.
the full HTTP request data is base64 encoded within the `reqeust` tag.
can use xpup go library/CLI tool to parse the XML.

- get raw HTTP request data from burp saved request - code from `get-burp-http-saved-request.sh`
```bash
cat $1 | go run github.com/ericchiang/xpup@latest //request | base64 -d
```

# getting HTTP content
the HTTP request has headers and optional content, if it is a POST request.
the headers all occur before the first blank line.
in order to retrieve the content, we can use a simple AWK script `get-http-post-content.awk`
```awk
BEGIN { content=0 }
content { print }
/^$/ { content=1 }
```

# decoding URL encoded post params
a simple python script can decode the HTML post parameters.
HTTP post parameters are separated by ampersand and equal characters.
the `unquote` function from `urllib` can help unescaping strings.
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

# decoding cookies
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

# script to generate python code
this calls other dependent scripts.

```bash

xpup="go run github.com/ericchiang/xpup@latest"
file="$1"
http_request=$(mktemp)

# parse burpsuite saved request file and convert to python requests script for copying
cat $file | $xpup '//request' | base64 -d > $http_request
dos2unix $http_request 2>/dev/null

url=$(cat $file | $xpup //url)
ct=$(awk '/^Content-Type/ { print $2 }' $http_request)
method=$(awk 'NR == 1 { print $1 }' $http_request)
getparams=$(echo $url | $ENVIRON_BASEPATH/zet/20230928133216/get-url-params.py)
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

cat << HEADER
import requests

url = "$url"
cookies = $cookies

## optional
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

cat << FOOTER
print(r.text)
FOOTER

rm $http_request
```
