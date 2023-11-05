+++
title = 'Testing Recurrent Openai Chatgpt Prompts'
date = 2023-11-05T01:05:45-05:00
draft = false
+++

# creating your own assistant with your own data
- you can create an assistant with your own data. here is how: https://youtu.be/9AXP7tCI9PI?si=dOd8TuYSDxM5Ke79
- link to example python project from video https://github.com/techleadhd/chatgpt-retrieval

## crafting prompts
you can make pretty complicated assistants just by crafting GPT prompts.
the example assistant scripts make use of python langchain library to load your own data to create an assistant and encode it somehow into the prompt.

an even simpler example of this is to create a stateful assistant with memory just by feeding the history back in with the prompt, kind of like how it works with a recurrent neural network, except with just text content.
include a conversation log of everything that was talked about previously with each prompt in a markdown snippet, and explain to ChatGPT that the chat history is included for context.
the conversation history could also even be periodically summarized through ChatGPT in order to keep the prompt smaller, sort of like encoding long term memories by editing the text.

## picking a CLI tool
first I tried [openai-cli](https://github.com/peterdemin/openai-cli).
this seems like a quick and dirty proof of concept project.
it has default model and does not let you change it!
I want to use `gpt-4` as the model.

next, I looked for CLI tools where you can configure the model to use.
the CLI tool version I ended up using is [gpt-cli](https://github.com/kharvd/gpt-cli).
- installing with pip
```bash
pip3 install gpt-command-line
```

finally, i created a wrapper tool to call the CLI tool with the appropriate command line arguments and model so that i can run ChatGPT on hightlighted text within [vim](https://github.com/nicholas-long/environment/tree/main/zet/20230905015059).
- [CLI tool notes and wrapper script](https://github.com/nicholas-long/environment/blob/main/zet/20231103204105/README.md)

# making a friend assistant
i tested out this idea in order to [create a chatbot assistant friend](https://github.com/nicholas-long/environment/blob/main/zet/20231102221207/README.md) for my kids.
i obviously don't trust this enough to let kids play with it without supervision yet, so i tried interacting with the chatbot with the kind of questions it might get asked, like the kind of stuff my kids repeatedly ask.
i learned from doing this that you need to explain what is going on in the chat history so that it doesn't answer questions twice or get confused about who said what.
i asked it what it should be named, and it chose the name "Friend bot".

## structure of recurrent prompt assistant
- header passed into every prompt is stored in file called `prime-directive`
- `generate-prompt` is the script to generate the whole prompt each time
- `run` is the script to run each prompt
