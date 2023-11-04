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

These are my thoughts that have been collected over the course of working on and using the project.

- [zkvr](https://github.com/nicholas-long/zkvr) is a functioning graph database written in like 350 lines of bash and awk
  - it would work as a graph database in a really low overhead docker type of environment or embedded system.
  - it is easier to script something for a lot of nodes than to play with a graph UI visualizer
- need to have places to write down ideas and be able to create new places when new kinds of ideas arise
- issue tracking - it is possible to keep track of issues using the links within cards relative to other cards.
  - a list - cards attached to a specific card can be considered "in a list". for example, a list of cards that need something fixed
  - cards in a chain - a workflow process can store an index or position
  - the issues themselves can have "pointers" or "lists" of other cards to work on
  - able to leave off and come back at any point
  - link pull requests to cards - dope idea from [rwxrob](https://github.com/rwxrob)
    - can edit the card
    - pull request link shows up cool in github
    - the URL contains information about the id of the request even if the cards are moved elsewhere
- links improve the content by adding context
- some links should be one-way links
  - zkvr uses 2-way links within the references, but some links should be one way. links in the document are not processed by zkvr for backlinks.
  - people are good at reading context but bad at navigating complicated UIs until they have a much higher level of experience with them, so links to documentation should be two way or you will get lost and waste time
- lines of text travel across links as projects take place, kind of organically. the links are useful to help facilitate transferring lines.
- creating a zettelkasten from existing markdown documents: converting files from markdown hierarchy of headings
  - creates really sparsely linked cards - needs way more links!
- if you want some hierarchies and directory structure to find things while working on something in the terminal, make some symlinks to the zet directories
- numbering sequentially based on timestamps can be an ordering imposed on zettels
  - if you can look at all the unique IDs of cards you created in order, the titles form a journal where you can click for context.
- graph searching concepts
  - if i want to get to a topic, i know a good path to get there through these links. it forms something like a "mind palace".
  - i can be working on one thing and get to any other notes with 2-5 clicks
  - only have to remember which things are connected together to navigate the "UI"
- tags are already like concept hubs, so it seems silly to make concept hubs and tags for things
  - can already query them in that way
  - the main difference is not being able to write notes on a tag, so if you want to write about it then use concepts instead?
  - should use hubs for specific things or instances and tags for a general property - what it is
  - i had not picked one yet so i've been doing both tags and hubs, and querying and using tags seems very reasonable, while concept hubs introduce tons of litter
- [graph query language](https://github.com/nicholas-long/zkvr/blob/main/zet/20221013221136/README.md)

