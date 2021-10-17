---
layout: post
title: Make your own blog with jekyll
category: Other
---

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
jekyll new MyBlog
```
Detail:

Jekyll Tutorials: https://jekyllrb.com/tutorials/home/ 

#### Load error: cannot load such file – webrick
```bash
bundle add webrick
```