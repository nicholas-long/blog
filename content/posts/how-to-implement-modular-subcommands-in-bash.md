+++
title = 'How to Implement Modular Subcommands in Bash'
date = 2023-11-16T14:22:18-06:00
draft = false
+++

# how to implement a modular subcommand with lightweight scripts

Creating powerful Command Line Interface (CLI) tools quickly can be achieved through a method that focuses on organizing your code. This approach involves a modular design that is not only easy to test but also allows for the separation of concerns. Subcommands, which can be written in whatever language appropriate for their specific job, play a significant role in this process.

Common implementation functions can be transformed into subcommands, providing the ability to hide them when necessary. A real-world example of this is the `git` command. Developers can leverage numerous implementation subcommands within `git` to retrieve text-formatted data regarding files within a repository.

Interestingly, implementing a command with subcommands can be a simple process. It can be executed either as a set of short scripts or within a compiled program. Both methods provide reliable results, thereby giving developers the flexibility to choose an approach that best suits their project needs.

## steps to implement
In Bash, creating a CLI tool with subcommands involves a series of steps. The primary command you execute is a wrapper script. This script manages file paths and locates the implementation of subcommands. It needs to be located in the `PATH` environment variable, but the subcommands do not require such locating. Typically, for installed programs, this wrapper could be installed in `/usr/bin` and be designed to locate subcommand files installed in other locations, for example, `/usr/share`.

To pass to subcommands, you should set an environment variable to store the main program command `$0`. This way, subcommands can call each other using the main wrapper script. If there’s logic involved in finding subcommand scripts, it's more convenient to have the parent wrapper script responsible for finding them.

Finally, you will have to switch based on the subcommand name. There are two options for this. One is a single, giant switch statement. This might be suitable for small projects, but it can become lengthy and complicated as the project grows. The other option is to have separate files with the subcommand as the file name. This approach helps to keep it clean and manageable, especially for larger projects.

## example Bash script implementation
Here, we'll walk through a sample bash script that demonstrates how to create a straightforward subcommand, with each step of the code thoroughly documented in the comments.
```bash
# get directory where current script is located
SCRIPT_DIR=$( cd -- "$( dirname -- "${BASH_SOURCE[0]}" )" &> /dev/null && pwd )

# save the current command to an environment variable so subcommands can call other subcommands easily by running this wrapper script
export prog="$0"

# if this current script running is a symlink then we have to follow it to find subcommands
if [ -L "$0" ]; then
  realdir=$(dirname $(realpath "$0") )
  subcommand_dir="$realdir/subcommands"
else
  subcommand_dir="$SCRIPT_DIR/subcommands"
fi

subcommand="$1"
shift
if [ -z "$subcommand" ]; then # if args are empty then show help - common case
  subcommand="--help"
elif [[ "$subcommand" == "-h" ]]; then
  subcommand="--help"
fi

if [[ "$subcommand" == "--help" ]]; then
  echo "Usage: $0 [ subcommand ] ..."
  echo "Subcommand Options:"
  # list out all available subcommands
  ls "$subcommand_dir"
  exit 1
else
  "$subcommand_dir/$subcommand" "$@" # run subcommand with all arguments passed in
fi
```

## example implementations of commands with subcommands
- [zkvr and its CLI command]({{< ref 'zettelkasten-github-tui-zkvr-project.md' >}})
- [fsdb partitioned filesystem database]({{< ref 'partitioned-event-database-on-filesystem.md' >}})

