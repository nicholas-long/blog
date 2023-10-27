+++
title = 'About This Blog and Setting It Up'
date = 2023-10-25T21:50:56-05:00
draft = false
+++

# About this Blog

## Why I Needed A Blog
I am setting up my blog in order to accomplish multiple things.
I need a website to put on my resume, and it would be good practice to develop and work on deployment scripts.
I could also really use a website to write about all the notes i’ve take on hacking, 3d printing, and data engineering, and maybe i could even use a storefront for custom 3D prints.
I am also testing integration with github actions, cron jobs, and webhooks in order to automate the deployment of this site.

## Testing Jekyll
I initially tried jekyll. 
As of 2023-10-25, it was not working when installed from ruby gems or from the apt package manager.
There was a dependency issue with one of the gems.
I even tried running it in a docker, no dice.
That's fine; I was not tied to any particular framework yet.
I want to make sure that any markdown website generator I use will be portable enough to build the page on a server, and jekyll seems too sketchy.

## Creating with Hugo
- the [blog source](https://github.com/nicholas-long/blog) is avaialable to view on github

I am testing hugo.
I like editing markdown in vim, so it should be a pretty good framework to use for my blog.
- Hugo quick start guide: https://gohugo.io/getting-started/quick-start/
  - installing hugo
  ```bash
  git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
  echo "theme = 'ananke'" >> hugo.toml
  ```

# Setting Up the Crons

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

## changing from crons to webhooks
After the initial setup with cron, I decided to implement [webhooks]({{< ref "github-blog-publish-webhooks" >}}).

# How to do Links in Hugo
I like Hugo so far. It seems relatively easy to add images and content.
One thing that seems to be an issue is links. Hugo has a reference system for dealing with this problem.
It uses a template-language-like syntax to update links to point to the proper paths.

- links within hugo
```markdown
testing links within hugo [link text]({{< ref "third" >}})
```
