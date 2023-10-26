+++
title = 'My Terminal Environment and Dotfiles'
date = 2023-10-26T15:03:29-05:00
draft = false
+++

# what is this environment

- link to environment: https://github.com/nicholas-long/environment
  - permalink to [post about this blog](https://github.com/nicholas-long/environment/tree/main/zet/20231025215645)

## using zettelkasten as a graph database for storing programs
- having it structured like a zettelkasten makes it feel like it is coming alive
This seems like the best way to store and work with all of my scripts while maintaining notes about them.
The idea is that scripts and projects start expanding and growing in complexity, but they also tend to tie into each other.

I can have notes about developing a script, example commands for running the script, and the script itself, all tied together in one place.
The documentation pages itself and the bidirectional links between them can indicate dependencies between scripts.
These dependencies between scripts are clear when the documentation page is a first-class component of the project.
If you get lost looking for a script, you can browse around the context for what is related to it.
Every README document within the directory can also have tags which are searchable. Indexes to the tags are also stored as valid markdown pages.

### using zkvr
- link to zkvr: 
I developed the project [zkvr](https://github.com/nicholas-long/zkvr) in 2022 after learning about zettelkasten.
I watched [rwxrob](https://github.com/rwxrob)'s youtube for inspiration for the structure of the project.
I took the ideas and ran with them, creating an fzf menu-driven workflow script for maintaining a graph database of markdown files.
I even created a simple [graph query language](https://github.com/nicholas-long/zkvr/blob/main/zet/20221013221136/README.md) for it that can be used to find a path of document nodes connected to other nodes, filtering by tags.
