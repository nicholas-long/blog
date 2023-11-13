+++
title = 'Platform Specific Version Selector Install Scripts'
date = 2023-11-12T17:54:32-06:00
draft = false
+++

# creating platform specific install scripts for my environment
My [environment]({{< ref "my-terminal-environment-and-dotfiles.md" >}}) and dotfiles are installed automatically when the [install scripts](https://github.com/nicholas-long/environment/blob/main/zet/20230905015223/README.md) are run.
These scripts will run other dependent scripts designed as version selectors for programs where there is no common apt or brew installation package candidate.

There are two such programs:
- [lazygit version selector and interactive install script](https://github.com/nicholas-long/environment/blob/main/zet/20230922051930/README.md)
- [bat version selector script](https://github.com/nicholas-long/environment/blob/main/zet/20230907151050/README.md)

`bat` is a command to preview and pretty print code or markdown with syntax highlighting.
It has an installation package in apt on some debian systems, but [the apt package for bat does not actually seem to work](https://github.com/nicholas-long/environment/blob/main/zet/20230905021249/README.md).
I have started downloading the release package directly from github.

## script to get release links from github
In order to create the version selectors and download releases from github, I first needed to create a [script to get all the github release links for the latest release](https://github.com/nicholas-long/environment/blob/main/zet/20230905030303/README.md) of a repository.
I can do this with two API calls from curl within bash scripts.

## selecting version based on architecture
This is a excerpt from a script to select the release link for the architecture.
It takes the architecture string of your CPU and finds a corresponding release link from bat.
```bash
# select architecture and convert to names used in bat releases
export desired_arch=$( lscpu |  awk '/Architecture/ { print $2 }' | awk '
  /aarch/ { print "arm64" }
  /arm.*64/ { print "arm64" }
  /arm.*32/ { print "armhf" } # test this - is this right?
  /x.*64/ { print "amd64" }
' )

if [ -z "$desired_arch" ]; then
  echo "could not interpret this architecture version."
  exit 1
fi

# find the url that corresponds to the selected architecture from above
download_url=$(zet/20230905030303/github-latest-files https://github.com/sharkdp/bat | grep -v musl | awk '$1 ~ /deb$/' | awk '
  $1 ~ ENVIRON["desired_arch"] { print $2 }
')
```
