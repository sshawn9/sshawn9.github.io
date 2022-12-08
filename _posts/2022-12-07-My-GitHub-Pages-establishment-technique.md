---
title: "My GitHub Pages"
tags:
  - GitHub Pages
  - Jekyll
---

## Preface
Here is a record of my GitHub Pages establishment technique based on [Jekyll](https://jekyllrb.com/) and the [minimal-mistakes](https://github.com/mmistakes/minimal-mistakes) theme.  
I decided to make this work years ago while nothing was accomplished for practical reasons, and I am glad to write this as my first post.  
Check my source code of this site at [here](https://github.com/sshawn9/sshawn9.github.io).  

## Technique details
### Jekyll
As GitHub recommends, I use Jekyll which is a static site generator. For basic concepts learning, following the [step by step tutorial](https://jekyllrb.com/docs/step-by-step/01-setup/) would be a good choice. In later development, using [Jekyll docker image](https://hub.docker.com/r/jekyll/jekyll/) for building and local hosting.  

```shell
# start a container from Jekyll docker image
# port/4000 for local hosting at http://localhost:4000
# port/35729 is exposed by image
docker run -itd -p 35729:35729 -p 4000:4000 -v $HOME/workspace/sshawn9.github.io:/jekyll --name jekyll jekyll/jekyll

# enter the container
docker exec -it jekyll bash

# some commonly used commands in Jekyll container
bundle init # create the default Genfile
bundle
bundle update
jekyll serve
```

### minimal-mistakes theme  
Instead of starting from scratch, choosing a theme is more  convenient as you can focus on writing without concerning about the layouts, styles and so on.  
I have been seeking for an elegant theme while which satisfies me is not free. Here are some theme websites:  
- <http://jekyllthemes.org/>
- <https://jekyllthemes.io/>  

Finally I back to [minimal-mistakes](https://github.com/mmistakes/minimal-mistakes) which is widely known and free under MIT license.  
You can start with the theme template [mm-github-pages-starter](https://github.com/mmistakes/mm-github-pages-starter) to create your own GitHub Pages repo.  
![](/assets/images/start-with-the-theme-template.png)
Do not miss the [troubleshooting](https://github.com/mmistakes/mm-github-pages-starter#troubleshooting)  
When you try to host your website from this template locally, you may encounter following liquid exception:
![](/assets/images/liquid-exception.png)  
To fix it:  
1. Add following lines to your `_config.yml`.
```shell
# host locally:
# modify as your own setting
PAGES_REPO_NWO: sshawn9/sshawn9.github.io
repository: sshawn9/sshawn9.github.io
```
2. Add following to your `Gemfile`.
```shell
group :jekyll_plugins do
  gem "kramdown-parser-gfm"
  gem "webrick"
end
```  

Refer to [here](https://github.com/jekyll/github-metadata/blob/main/docs/configuration.md#configuration) for more information.  
And I prefer to ignore the warning of `No GitHub API authentication could be found. Some fields may be missing or have incorrect data.`

### Deploy to Github
There is ways to do this seeing [here](https://jekyllrb.com/docs/deployment/), and I select GitHub Actions.  
[My GitHub Action for deployment.](https://github.com/sshawn9/sshawn9.github.io/blob/main/.github/workflows/jekyll-gh-pages.yml)  

## More info
- [Minimal Mistakes GitHub Pages site starter demo site](https://mmistakes.github.io/mm-github-pages-starter/)
- [github-metadata](https://github.com/jekyll/github-metadata)
- [WEBrick](https://jekyllrb.com/docs/configuration/webrick/)

## favicon
Make your own `_includes/head/custom.html`. Refer to [here](https://github.com/mmistakes/minimal-mistakes/blob/master/_includes/head/custom.html)  

## TODO
- [ ] remove footer with feed
- [ ] try different default layouts
- [ ] [Post with Table of Contents](https://mmistakes.github.io/minimal-mistakes/layout-table-of-contents-post/)
- [ ] [header video](https://mmistakes.github.io/minimal-mistakes/layout/uncategorized/layout-header-video/), header image and so on
- [ ] custimized sidebar like [this](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)
- [ ] check search engine optimization
