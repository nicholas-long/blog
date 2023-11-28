+++
title = 'Script to Extract Text From Every Second of Youtube Videos Frames'
date = 2023-11-26T17:47:07-06:00
draft = false
+++

- Link to [script to OCR text content displayed on the screen of youtube videos](https://github.com/nicholas-long/environment/blob/main/zet/20231007033903/README.md)

# Motivation for Project
It would be neat to be able to search the text content displayed on the screen of any youtube videos.
In order to do that, you would have to run optical character recognition (OCR) on the frames of the video.

I watch a lot of hacking walkthroughs from [ippsec](https://www.youtube.com/@ippsec) where most of the videos feature terminal output.
Searching this content might be useful in order to find examples of using a particlar hacking tool or concept.
Searching is actually already pretty easy with ippsec's videos in particular, because there is so much annotations added to the videos, and they are all searchable on [ippsec's website](https://ippsec.rocks/).

But I want to create a CLI tool that can convert a video to text for later processing.
It should print a line with the current timestamp followed by the text content of the video at that frame.
It is also possible to watch the video within the terminal if you can time the conversion process so that the text appears where it would at roughly the same time.

# Interesting Combination
As well as an interesting computer science experiment, this project also got me a little experience doing some solutions engineering using several interesting programs.
This project is made possible by an interesting combination of programs.
- [youtube-dl](https://github.com/ytdl-org/youtube-dl) - a project to download youtube videos
- [ffmpeg](https://github.com/ytdl-org/youtube-dl) - record videos, convert videos, and extract frames from videos
- [tesseract](https://github.com/tesseract-ocr/tesseract) - OCR images

# Installing
It was awkward to install ffmpeg on my system.
I had to do a full upgrade and fix apt dependencies to install it.
This was simply because I had not updated in a while.

The `youtube-dl` command says it can be installed from pip, but that version might not work.
During my testing, the pip version did not work, but installing and running the fresh version from github did work.

- Installing `youtube-dl` from github source
```bash
git clone https://github.com/ytdl-org/youtube-dl
cd youtube-dl
sudo python3 setup.py install
```

# Running

- Testing on [ippsec video pilgrimage](https://www.youtube.com/watch?v=aaUlHicClrI)
```
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> 00:1:10
= seOreence ee

# Nmap 7.93 scan initiated Mon Nov 20 14:28:58 2023 as: nmap -sC -sV -oA nmap/pilgrimage 10.10.11.219
Nmap scan report for 10.10.11.219

Host is up (0.091s latency).

Not shown: 998 closed tcp ports (reset)

PORT STATE SERVICE VERSION

22/tcp open ssh OpenSSH 8.4p1 Debian 5+debllu1 (protocol 2.0)
| ssh-hostkey:

| 3072 20be60d295f628c1b7e9e81706f1683 (RSA)

| 256 Oeb6a6a8c99b4173746e70180d5feOaf (ECDSA)

|_ 256 d14e293c708669b4d72cc80b486e9804 (ED25519)

80/tcp open http nginx 1.18.0

|_http-title: Did not follow redirect to http://pilgrimage.htb/
|_http-server-header: nginx/1.18.0

Service Info: 0S: Linux; CPE: cpe:/o: Linux: Linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Nov 20 14:29:11 2023 -- 1 IP address (1 host up) scanned in 12.20 seconds

nmap/pilgrimage.nmap (END)
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> 00:1:11
```
As you can see, it creates quite accurate and searchable output.
It takes most of a second to run each frame on my cyberdeck, so it is kind of like watching the video in real time.
I can add an optional `sleep` in to make it feel more like watching a live video.

# Code
Let's take a look at the code.
It is a very simple wrapper around these three programs.
This bash script feels like I just duct taped 3 programs together, but it works.

First I get the video URL from the first argument and check if youtube-dl is installed.
`youtube-dl` may return a mkv file or an mp4 file.
I am not sure what causes this.
The tools `exiftool` and `ffmpeg` work for mkv and mp4 files, so I can account for the different file names but checking which one exists.
For convenience, the videos are renamed `video.mkv` or `video.mp4`.
```bash
#!/bin/bash

url="$1"

youtube-dl "$url"
mv *.mkv video.mkv
srcfile="video.mkv"

if [ ! -f $srcfile ]; then
  mv *.mp4 video.mp4
  srcfile="video.mp4"
fi
```

Next, I use exiftool to figure out the duration.
It produces output in the format `Duration: HH:MM:SS`, so i can parse that with AWK.
An interesting trick I am doing here is printing out a Bash script using AWK and then sourcing it into Bash using file redirection in order to get the hours, minutes, and seconds values.
```bash
source <(exiftool $srcfile | grep ^Duration | awk -F : '
/^Duration/ {
  print "seconds=" $NF
  print "minutes=" $(NF-1)
  print "hours=" ($(NF-2) + 1) - 1
} ')
```

Finally, I loop over seconds and minutes (and should loop over hours too).
For each second, I print a header and run `ffmpeg` to convert it to a 1024x768 image.
For convenience, I am calling the image `image.png` each time.
Then, I run tesseract on the file and print the output.
It is important to clean up the images and output so the programs do not fail to overwrite them on the next frame.
```bash
for m in $(seq 0 $minutes); do
  if [ $m -lt $minutes ]; then
    sec=60
  else
    sec=$minutes
  fi
  for s in $(seq 0 $sec); do
    echo ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> 00:$m:$s"
    ffmpeg -ss $m:$s -i $srcfile -frames 1 -s 1024x768 -f image2 image.png >/dev/null 2>/dev/null
    tesseract image.png output >/dev/null 2>/dev/null
    cat output.txt
    rm image.png
    rm output.txt
    #sleep 1
  done
done
rm video.mkv
```
