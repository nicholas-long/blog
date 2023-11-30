+++
title = 'FSDB: A Partitioned Event Sourced Database on Filesystem'
date = 2023-10-27T08:45:36-05:00
draft = false
+++

# FSDB project
- partitioned database that exists in a directory on the filesystem
- works by partitioning rows of text into files, essentially turning a directory into a giant hashmap with compressed partitions
- optional timestamps can be added to rows and searched (resolution: 1 second, epoch time)
- link to notes about the project: [partitioned filesystem database](https://github.com/nicholas-long/environment/blob/main/zet/20230929145418/README.md)
- project github: https://github.com/nicholas-long/fsdb

## justification for the project
I need something like Kafka that can store things in partitions.
Kafka itself needs multiple docker images, kafka and zookeeper, running Java virtual machines in order to run.
I want it to have a lightweight memory footprint and work easily from within scripts.

## example usage
```bash
fsdb init -p 50
zcat ~/vulnerable-hashes-downloads-2022-09-22.gz | fsdb load
echo "data size"
du -hs data
echo "lines in database"
fsdb print | wc -l
echo "calling reoptimize..."
fsdb reoptimize >/dev/null 2>/dev/null
echo "data size"
du -hs data
echo "lines in database"
fsdb print | wc -l
fsdb erase

# testing the code
┌──(parallels㉿kali-linux-2022-2)-[~/environment/zet/20231005174423]
└─$ ./test-reoptimize
initializing config
50 partitions ok
using 50 partitions
using 50 partitions
data size
56M     data
lines in database
1553008
calling reoptimize...
data size
52M     data
lines in database
1553008
```

## subcommands
- init - initialize and set up number of partitions
- ingest data - pipe it into standard input and an awk script will put it where it belongs
- search for one ID or multiple
- search by time ranges
- search for IDs missing from the database - set difference
- print all data
- compress subcommand - compress text files and append to gzip streams together. called by ingest when a partition gets too large
- remove duplicates
- `reoptimize` subcommand is to rewrite all compressed streams as continuous gzip streams to optimize space

## previous work
I developed something like FSDB before for my file hash search engine project.
At that point, I partitioned the lines by the letters within the file hash.
This would only work for stochastic text and not for normal strings.
In order to apply it for general use, I used a simple hash function.

## testing and results
I was able to put billions of rows in the database. It can be relatively slow to load, but lookup is very fast.
