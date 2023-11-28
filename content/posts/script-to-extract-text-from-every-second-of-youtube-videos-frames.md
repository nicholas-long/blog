+++
title = 'Script to Extract Text From Every Second of Youtube Videos Frames'
date = 2023-11-26T17:47:07-06:00
draft = true
+++

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

