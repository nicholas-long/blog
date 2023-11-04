+++
title = 'Zettelkasten Github TUI - ZKVR Project'
date = 2023-10-27T12:36:27-05:00
draft = false
tags = ['zettelkasten']
+++

# zkvr project
I developed the [zkvr](https://github.com/nicholas-long/zkvr) project in 2022 after learning about zettelkasten.
I developed it using bash and awk scripts and other open source TUI (Text User Interface) tools available.
I watched [rwxrob](https://github.com/rwxrob)'s youtube for inspiration for the structure of the project.
I took the ideas and ran with them, creating an fzf menu-driven workflow script for maintaining a graph database of markdown files.
I even created a simple but powerful [graph query language](https://github.com/nicholas-long/zkvr/blob/main/zet/20221013221136/README.md) for it that can be used to find a path of document nodes connected to other nodes, filtering by tags.

## design
Every card in the zettelkasten is a directory containing a `README.md` file.
Links between the markdown pages connect the directories together.
The idea is that you can browse around the markdown within the terminal in a text-user-interface interface somewhat like a browser (back button, can spawn new tabs if tmux is running, etc.)
You can copy text from snippets or inline code segments within the document directly to the tmux buffer because copying and pasting is probably the most important part of the workflow of taking notes.

It is constructed of simple linux scripts because I don't want to depend on a platform or technology forever because platforms and especially libraries don't last forever.
The main skeleton of the script was constructed using bash and awk scripts and by running the `fzf` fuzzy finder program in new and interesting ways.
- [testing out user interfaces in fzf](https://github.com/nicholas-long/environment/tree/main/zet/20230929023221)

The component text user interface programs that combine to form the zkvr workflow are:
- [vim](https://github.com/nicholas-long/environment/blob/main/zet/20230905015059/README.md)
- [tmux](https://github.com/nicholas-long/environment/tree/main/zet/20230905015107)
- [lazygit](https://github.com/nicholas-long/environment/blob/main/zet/20230922051930/README.md)
- fzf

### fzf
Fzf is a go program that is intended as fuzzy searching file finder, but can be used for scripting many workflow optimizations.
It is very useful for scripting things that require user interaction.
- fzf is useful for menus or hotkeys to select things from a list
- it can choose files from within scripts
- the preview menu can turn scripts into TUI command processors

# assorted findings about implementing zettelkasten

- [zkvr](https://github.com/nicholas-long/zkvr) is a low-overhead graph database written in Bash and Awk.
- Copying a subset of zet directories mirrors copying a vertex-induced subgraph.
- Git tracks file changes, useful for monitoring changes from enrichment processes.
- Zettelkasten allows creating new spaces for new ideas.
- Issue tracking is possible through links within cards.
- Links add context to content, some should be one-way.
- Lines of text move organically across links during project development.
- Creating a zettelkasten from existing markdown documents needs more links.
- Sequential numbering based on timestamps provides order.
- Structure allows quick navigation between notes with a couple links.
- Tags and concept hubs are similar but serve different purposes.
- [Graph query language](https://github.com/nicholas-long/zkvr/blob/main/zet/20221013221136/README.md) included.
