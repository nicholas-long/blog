+++
title = 'Script to Extract Text From Every Second of Youtube Videos Frames'
date = 2023-11-26T17:47:07-06:00
draft = true
+++

# Motivation for Project
It would be neat to be able to search any youtube content based on the text content displayed on the videos.
In order to do that, you would have to run optical character recognition (OCR) on the frames of the video.
As well as an interesting computer science experiment, this project also got me a little experience doing some solutions engineering using several interesting programs.

I watch a lot of hacking walkthroughs from [ippsec](https://www.youtube.com/@ippsec) where most of the videos feature terminal output.
Searching this content might be useful in order to find examples of using a particlar hacking tool or concept.
Searching is actually already pretty easy with ippsec's videos in particular, because there is so much annotations added to the videos, and they are all searchable on [ippsec's website](https://ippsec.rocks/).

# Interesting Combination
This project is made possible by an interesting combination of programs.
- [youtube-dl](https://github.com/ytdl-org/youtube-dl) - a project to download youtube videos
- [ffmpeg](https://github.com/ytdl-org/youtube-dl) - record videos, convert videos, and extract frames from videos
- [tesseract](https://github.com/tesseract-ocr/tesseract) - OCR images

# Installing
It was awkward to install ffmpeg on my system.
I had to do a full upgrade and fix apt dependencies to install it.
This was simply because I had not updated in a while.
