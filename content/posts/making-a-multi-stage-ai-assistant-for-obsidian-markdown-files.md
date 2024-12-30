+++
title = 'Making a Multi Stage AI Assistant for Answering Queries About Numerous Obsidian Markdown Files with No Vector Database'
date = 2024-12-30T13:52:49-06:00
draft = false
+++

The way I take notes is most often in Zettelkasten format in [Obsidian](https://obsidian.md).
I have thousands of individual markdown files which are linked together in a huge web.
I want to be able to query them without uploading all the files or entering them into a vector database.
My personal preference is to have timestamps in the filenames, so some queries could be answered using the filename and content together.

The simplest and most effective solution for this is to include the entire content of the markdown files, as they are relatively short.
Additionally, some files could link to other markdown files that contain relevant content.

After solving these issues, I also must make it work with dynamic input from an Apple iOS Shortcut so that I can use it from Siri.
This is a relatively useful hack that I use in order to use my workflow shortcuts from my iPhone.

The approach that I used to accomplish this is made of several phases:
- plug the question into a loose "markdown search engine" to select a list of potential input files.
  - this could potentially be done by asking chatgpt to come up with `grep` patterns and use the results to select files
- ask chatgpt to select files
- ask the question in a prompt, including the filenames and full markdown content of all selected files

# `grep` problems
Inital testing for asking chatgpt to come up with `grep` patterns did not prove fruitful. The idea seems practical, but it does not always match the desired content.
For example, for the question "where did i go out to eat on my honeymoon?", chatgpt came up wtih the following commands:
```bash
grep -i -e 'honeymoon' -e 'eat' -e 'dining' -e 'restaurant' -e 'went out' -e 'honeymoon dinner' -e 'honeymoon meal' -e 'honeymoon restaurant' -e 'honeymoon dining' -e 'special occasion dining' *.md
```
This was not adequate to find the actual file. The queries are too specific, return a lot of false positives, and do not respect links within files.
This led me to come up with an AWK-based search engine.

Another issue comes from commonly occuring words in the query. These are called "stopwords" in the Search Engine Optimization (SEO) field.
For our example query, the following words will return extraneous results: "where", "did", "i", "on".
These words must all be ignored from the query.
I was able to fix this by using a [publicly available list of stopwords](https://github.com/Alir3z4/stop-words/blob/master/english.txt).
I convert this to grep ignore patterns as follows:
```bash
head stopwords.grep
^'ll$
^'tis$
^'twas$
^'ve$
^10$
^39$
^a$
^a's$
^able$
^ableabout$
```

# AWK-based markdown search engine
My strategy for the search engine was to use the keywords to dynamically create an AWK program that scores each file based on keyword matches:

```bash
# the variable awkprogram is a temporary file containing the generated awk program
# the variable searchwords is a temp file containing the keywords after filtering out stopwords

# generate keyword scoring AWK script
awk '
{ print "FNR == 1 && FILENAME ~ /" $0 "/", "{ score += " (length($0) * 10) " }" } # add special score for keywords in filename, only once
{ print "FNR == 1 && FILENAME ~ / " $0 "[ .]/", "{ score += " (length($0) * 50) " }" } # add special score for keywords in filename, only once
{ print "/" $0 "/", "{ score += " length($0) "}" }
{ print "/#" $0 "/", "{ score += " 5 * length($0) "}" }
{ print "{ for (n = 0; n < NF; n++) if (tolower($n) == \042" $0 "\042) score += " (2 * length($0)) " }" }
' $searchwords >> $awkprogram

# append ending section to awk script
# the script scores the results and pipes the output to the sort command to print the highest values at the top
cat << EOF >> $awkprogram
/^#+ / { score = score * 2 } # make headings worth more
{ scores[FILENAME] += score; score = 0 }
END {
  OFS = "\t"
  for (k in scores) {
    print scores[k], k | "sort -nr | head -n 20" # print the highest values at the top
  }
}
EOF
```
This works to search the files and return the most relevant ones in order.

# prompt for selecting files
After we have the list of files, we pass this into ChatGPT to select the ones that appear most relevant for answering the question.
Instead of having it write out filenames and introducing the possibility of mistakes, I number the lines and ask it to only return numbers.
> given a query and a numbered list of files, return only the numbers for files which seem like they would be relevant for answering the query. return only the numbers, one per line, with no additional text.

```bash
chatgpt --model gpt-4o --role "given a query and a numbered list of files, return only the numbers for files which seem like they would be relevant for answering the query. return only the numbers, one per line, with no additional text."
```

# final prompt
Finally we must print out the list of files and pass it into ChatGPT.
In this contrived example, my notes about my honeymoon have special markdown links to print the content of the daily notes from days while I was on the trip.
To handle this, when printing the markdown files, we must also print out nested content "include" links (links in Obsidian starting with an exclamation point).

- The prompt format is as follows:
  - role
```
Your job is to answer a question given the content of markdown files passed in. The file names of the markdown files contain date and timestamps, which could be useful for answering the question.
```
  - prompt
```
Query: [ query here ]

File Name: [ filename ]
-----------------------

[ file content ]

[ ... repeat for all files ... ]
```

## Working Example of Answering Question
```bash
echo 'where did i go out to eat for my honeymoon?' | ./query 2>/dev/null
During your mini-honeymoon, you went out to eat at the following places:

1. **Wildwood Sports Bar and Grill** - Known for their really good French fries.
2. **Northern Taphouse** - Another dining spot you visited.
3. **Blue Steakhouse** - Described as "amazing" with mentions of lobster chowder and aged prime rib.
4. **Cadillac Ranch** - You had lunch here.
5. **Bussin Birria Tacos** - You praised the food here as the "best fast food ever," specifically enjoying a beef birria taco and quesadilla.

These venues were part of your culinary experiences during the trip.
```

## iOS shortcut
The iOS shortcut to power asking questions to this assistant is very simple.
It uses my [chisel server trick]({{< ref "connecting-to-ssh-from-anywhere-using-chisel-pivot-server.md" >}}) for publicly exposing an SSH port of a machine running anywhere.
This way, I am able to use the assistant as long as the machine is running and connected to any network or behind any firewall.
[Using SSH to as a secure "API" gateway]({{< ref "using-ssh-as-a-secure-api-gateway.md" >}}) to pass input to programs is a trick I overviewed in my previous blog post.
