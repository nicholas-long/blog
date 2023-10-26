+++
title = 'About This Blog and Setting It Up'
date = 2023-10-25T21:50:56-05:00
draft = false
+++

# About this Blog

## why i needed a blog
I am setting up my blog in order to accomplish multiple things.
I need a website to put on my resume, and it would be good practice to develop and work on deployment scripts.
I could also really use a website to write about all the notes i’ve take on hacking, 3d printing, and data engineering, and maybe i could even use a storefront for custom 3D prints.

## creating
I am testing integration with github actions, cron jobs, and webhooks in order to automate the deployment of this site.

- the [blog source](https://github.com/nicholas-long/blog) is avaialable to view on github

I am testing hugo.
I like editing markdown in vim, so it should be a pretty good framework to use for my blog.
- Hugo quick start guide: https://gohugo.io/getting-started/quick-start/
  - installing hugo
  ```bash
  git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
  echo "theme = 'ananke'" >> hugo.toml
  ```

# setting up the crons

- testing with one minute
```bash
crontab -l
* * * * * cd ~/blog && ./pull-publish
```

One minute was fine for testing, but I think that is too frequent for actual use.

- changing to five minutes
```crontab
*/5 * * * * cd ~/blog && ./pull-publish
```
