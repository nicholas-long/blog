+++
title = 'Making ChatGPT Robots With iOS Shortcuts'
date = 2024-05-28T03:18:08Z
draft = true
+++

# intro
- prompt engineering
- use shortcuts to call APIs or run services within other apps
  - actions within shortcuts running from Siri need to return within 15 seconds (per action?)
- install ChatGPT app
- defualt iOS shortcut action installed: "ask chatgpt" which starts a two-way dialogue
  - but once again: actions within shortcuts running from Siri need to return within 15 seconds (per action?)
- [ ] get screenshots of shortcut

# shortcut
- do prompt engineering in text box or in place to chatgpt
- tell it to be brief and keep answer to 1 paragraph
  - speeds up response
  - chatgpt has a tendency to be very verbose - thinking out loud?

# future endeavors
- document search
  - something like langchain for searching the documents, except doing 2-step querying
  - for example, which of these documents would a LLM need to answer the following question...?
  - run script over ssh to do deep searching of documents
