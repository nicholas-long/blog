+++
title = 'Automatically Tagging Zettelkasten Documents With Chatgpt'
date = 2023-11-24T00:14:09-06:00
draft = true
+++

# Project to Automatically Select Tags for Zettelkasten Documents

## Motivation for Project
- it is easy to forget to tag documents
- it is easy to forget about specific tags that would be relevant at the time
- sometimes it is a good idea to come up with new tags?

- potential downsides
  - maybe in some people's implementations, some tags are reserved
  - misunderstanding the context

# code
## generate prompt for chatgpt
```bash
#!/bin/bash

echo "Using the following set of hashtags,"
cat $(root-knowledge-base-repo-path)/all_tags
echo 'choose an appropriate set of at most five tags for the following markdown document:'
echo ''

cat "$1"
```

## generate tags for document passed as argument
```bash
#!/bin/bash

# get current script directory
SCRIPT_DIR=$( cd -- "$( dirname -- "${BASH_SOURCE[0]}" )" &> /dev/null && pwd )

# use a grep pattern to pull out tags to keep output format consistent
$SCRIPT_DIR/generate-prompt "$1" | gpt --model gpt-3.5-turbo --prompt - | grep -Eo '#[A-Za-z]+' | sed 's/^#//g'
```
