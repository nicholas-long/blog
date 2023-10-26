+++
title = 'About This Blog and Setting It Up'
date = 2023-10-25T21:50:56-05:00
draft = false
+++

# About this Blog

I am setting up my blog.
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

I want the
```bash
crontab -l
*/5 * * * * cd ~/blog && ./pull-publish
```
