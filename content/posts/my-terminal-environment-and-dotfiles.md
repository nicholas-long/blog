+++
title = 'My Terminal Environment and Dotfiles'
date = 2023-10-26T15:03:29-05:00
draft = false
categories = ['linux', 'terminal', 'environment', 'dotfiles', 'scripts', 'zettelkasten']
+++

# what is my environment repository?

- link to environment: https://github.com/nicholas-long/environment
  - permalink to [post about this blog](https://github.com/nicholas-long/environment/tree/main/zet/20231025215645)

I made a zettelkasten for dotfiles and scripts.
Maintaining and working within it feels like being plugged directly into an enormous and ever-changing project.

## what i put in the environment
- vim dotfiles
- tmux dotfiles
- bashrc and zshrc configuration scripts
- useful scripts i need or come up with for specific purposes
- shortcut commands that are available on the command line
- install scripts to set it all up on multiple platforms and architectures

## using zettelkasten as a graph database for storing programs

Having it structured like a zettelkasten makes it feel like it is coming alive.
Zettelkasten seems like the best way to store and work with all of my scripts while maintaining notes about them.
The idea is that scripts and projects start expanding and growing in complexity, but they also tend to tie into each other.

I can have notes about developing a script, example commands for running the script, and the script itself, all tied together in one place.
The documentation pages itself and the bidirectional links between them can indicate dependencies between scripts.
These dependencies between scripts are clear when the documentation page is a first-class component of the project.
If you get lost looking for a script, you can browse around the context for what is related to it.
Every README document within the directory can also have tags which are searchable. Indexes to the tags are also stored as valid markdown pages.

### using zkvr
I developed the [zkvr](https://github.com/nicholas-long/zkvr) project in 2022 after learning about zettelkasten.
I developed it using bash and awk scripts and other open source TUI (Text User Interface) tools available.
I watched [rwxrob](https://github.com/rwxrob)'s youtube for inspiration for the structure of the project.
I took the ideas and ran with them, creating an fzf menu-driven workflow script for maintaining a graph database of markdown files.
I even created a simple [graph query language](https://github.com/nicholas-long/zkvr/blob/main/zet/20221013221136/README.md) for it that can be used to find a path of document nodes connected to other nodes, filtering by tags.
- link to [blog post about zkvr]({{< ref "zettelkasten-github-tui-zkvr-project" >}})

This ended up being the perfect combination for storing my dotfiles and scripts.
Some of the scripts are even related to other content or scripts within the repostiory.
I am calling it my "third brain" because people refer to zettelkasten as a second brain, and I already use Obsidian for that.
The difference and advantage using zkvr over Obsidian for code projects is that it is designed around packaging files together and treating that package as a cohesive unit with outgoing links to other relevant units.

