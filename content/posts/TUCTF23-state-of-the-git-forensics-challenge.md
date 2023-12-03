+++
title = 'TUCTF23 State of the Git Forensics Challenge'
date = 2023-12-03T15:05:22-06:00
draft = false
+++

- CTF link: https://ctfd.tuctf.com/challenges
- challenge files: https://github.com/nicholas-long/environment/blob/main/zet/20231203212512/README.md

# Solving the TUCTF23 State of the Git Challenge

The TUCTF23 challenge took place recently and ended on 2023-12-03.
In it, there was a forensics challenge which included some git commands and some data analysis.
The challenge says we should check if any secrets are exposed in their git repository.

This challenge comes as a `tar.gz` file.
Because of the name and nature of the challenge, I immediately checked if it was a git repository, and it was.

```bash
tar -xzvf tuctf-devops-2023.tar.gz
git log
```

There are pages and pages of commits.
A quick scan of the files shows that they all contain garbage base64 code.
One of the files appears to be a yaml terraform config, but it is cut off in the middle of the file.

The obvious next step is to go check through the git history to see if there are any secrets exposed.
Since I know I am looking for a flag, I can try checking out each commit and grepping for flags.
I do this by grabbing all commit lines from the git log and checking them out and running grep.

```bash
git log | awk '$1 == "commit" { print $2 }' | while read commit; do
  git checkout $commit
  grep -Ri TUCTF .
done > /tmp/runoutput2
```

Thousands of results! Oh no!
It turns out the yaml file contains a lot of stuff that looks like flags.
Trying to include curly braces to help narrow it down does not help.
Time to go back to the drawing board.

Since there are so many files and so many commits, I should try looking through and analyzing all of the files in git in one go.
I can run a series of file analysis commands on all of them, reformat it to a table, and find anything that looks weird.

## Analyzing Git Blobs

- How to get a list of all the blobs on git: https://stackoverflow.com/questions/1595631/how-to-get-a-list-of-all-blobs-in-a-repository-in-git
  - working on this line a bit yields something where I can grab all blobs with awk
```bash
git rev-list --objects --all | git cat-file --batch-check='%(objectname) %(objecttype) %(rest)'

$ git rev-list --objects --all | git cat-file --batch-check='%(objectname) %(objecttype) %(rest)' | awk '$2 == "blob" { print $1 }' | wc -l
3065
```

Some common file analysis commands I can run to analyze are `wc` to count words or lines and `file` to analyze the type of each file blob.
A blob in git contains the entire contents of a file, and you access it in git based on a hash.
This code is grabbing all the git hashes of files and piping them into a while loop to run the analysis commands:
```bash
git rev-list --objects --all | git cat-file --batch-check='%(objectname) %(objecttype) %(rest)' | awk '$2 == "blob" { print $1 }' | while read line; do
  echo $line
  git cat-file -p $line | wc -l
  git cat-file -p $line | file -
done > ../gitfilereport
```

Doing this yields plenty of data about each file.
I can combine it together into rows of tab-separated data using the `paste` command.
Doing so, it becomes clear that there are a lot of files that have the same lengths.
Most files have the lengths 127, 0 (which is one line with no newline), or 1.
This seems like a reference to `127.0.0.1`.

Removing all files with 127, 0, or 1 lines yields one remaining file:
```bash
$ git cat-file -p 955de908450115ce7b6892c2a58c03e7e2221a6f
```

This file is another yaml config, but a complete one that contains a password.
The password is a base64 encoded version of the final flag, which explains why it could not be found simply with grep.

