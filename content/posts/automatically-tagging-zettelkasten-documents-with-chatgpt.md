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

Sometimes this code returns duplicate tags, or returns some of the same tags that already exist.
I was able to reuse a script that I created for removing duplicate tags during the process of [implementing the merging functionality]({{< ref "adding-merge-note-feature-to-workflow.md" >}})

## generating the prompt for chatgpt
First, I created a simple script for generating the prompt for ChatGPT.
It simply needs to provide:
- a list of tags that the assistant can select from
- some text around the prompt to provide the instructions
- the document

I could do some further prompt engineering here if I start having problems with the tags it returns.

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

# results
- sometimes it gets creative and adds extra tags that do not exist yet
