---
layout: post
title: Make your own blog with jekyll
category: Other
---

Building a website nowadays is not as hard or expensive as it was in the past. These days, you do not have to learn a crazy programming language to have your site up and running.

#### Install Jekyll

```bash
# First install rbenv
brew install rbenv

# Second install ruby 2.7
# Why, detaill see: https://talk.jekyllrb.com/t/error-no-implicit-conversion-of-hash-into-integer/5890
rbenv install 2.7.4

# If you're using Zsh
echo 'export PATH="$HOME/.rbenv/versions/2.7.4/bin:$PATH"' >> ~/.zshrc

# If you're using Bash
echo 'export PATH="$HOME/.rbenv/versions/2.7.4/bin:$PATH"' >> ~/.bash_profile

# Install ruby 2.7.4
rbenv global 2.7.4

# Check your gem version
gem version

# Restar your terminal, then
gem install --user-install bundler jekyll
```

#### Creating a Site

```bash
# Jekyll Tutorials: https://jekyllrb.com/tutorials/home/
jekyll new MyBlog
```

#### GitHubRepo Setting

Create a repository, the repo name is `{username}.github.io`

Add your site to the repo, detail see: https://gist.github.com/code4you2021/59822bd98499422f5b7d5de6f70cf2d9

Setting the GitHubPage:
![wR4w8T](https://cdn.jsdelivr.net/gh/code4you2021/oss@main/uPic/wR4w8T.jpg)

#### Load error: cannot load such file – webrick

```bash
bundle add webrick
```
