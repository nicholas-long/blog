+++
title = 'Using ChatGPT to Automatically Tag Documents in a Zettelkasten Document Graph'
date = 2023-11-24T00:14:09-06:00
draft = true
+++

# Project to Automatically Select Tags for Zettelkasten Documents

## Motivation for the Project
When working within a zettelkasten, it's not uncommon to neglect the task of tagging documents. This can be attributed to the speed of creating a new card, leaving little time to tag it properly. Additionally, it's easy to overlook certain tags that may be relevant at that moment. There's also the consideration of whether it would be beneficial to create new tags.

However, there are potential downsides to this approach. For instance, in some people's implementations, certain tags might be reserved, creating a potential for confusion. Additionally, the AI might misinterpret the context, leading to inaccuracies.

## Results
Moving onto the results, the system can occasionally surprise you with its creativity by adding tags that don't yet exist. The time it takes to run for each document is relatively short, only a second. However, it's not always accurate, at times adding tags that are only loosely related to the content. Despite these minor drawbacks, it maintains a good level of accuracy overall.

# Code

Sometimes this code returns duplicate tags, or returns some of the same tags that already exist.

## Generating the Prompt for ChatGPT
First, I created a simple script for generating the prompt for ChatGPT.
It simply needs to provide:
- a list of tags that the assistant can select from
- some text around the prompt to provide the instructions
- the document
- a constraint on the number of tags returned.
  - if this is not constrained, it might return 2 or it might return 20. it would be annoying to clean up if it added too many.

I could do some further prompt engineering here if I start having problems with the tags it returns.

```bash
#!/bin/bash

echo "Using the following set of hashtags,"
cat $(root-knowledge-base-repo-path)/all_tags
echo 'choose an appropriate set of at most five tags for the following markdown document:'
echo ''

cat "$1"
```

## Generate Tags for Document Passed as Argument

I used the gpt CLI command tool that I [selected for use in the terminal]({{< ref "testing-recurrent-openai-prompts.md" >}})

```bash
#!/bin/bash

# get current script directory
SCRIPT_DIR=$( cd -- "$( dirname -- "${BASH_SOURCE[0]}" )" &> /dev/null && pwd )

# use a grep pattern to pull out tags to keep output format consistent
$SCRIPT_DIR/generate-prompt "$1" | gpt --model gpt-3.5-turbo --prompt - | grep -Eo '#[A-Za-z]+' | sed 's/^#//g'
```

## Implementing it in ZKVR

I wanted to implement this as a feature within the [zkvr project]({{< ref "zettelkasten-github-tui-zkvr-project.md" >}}).
I have a couple ideas for AI powered scripts that could run within an interconnected graph of Zettelkasten documents.
It might be a good idea to add some additional configuration steps to zkvr to setup a ChatGPT API key so the cool features work if someone else uses zkvr.

I added a new command to the program called `autotag`. It will automatically add tags to the current document.
I was able to reuse a script that I created for removing duplicate tags during the process of [implementing the merging functionality]({{< ref "adding-merge-note-feature-to-workflow.md" >}})

- example of programmatically adding tags to a card by ID from scripts using zkvr's CLI tools
```bash
./zc addtag -t test 20231114070621
```

- [ ] finish this
