---
layout: post
title:  "Quirky VIM things"

categories:
  - Linux

tags:
  - Vim
  - Editor
  - Macos
  - Linux


excerpt: Vim on macos!
excerpt_separator:  <!--more-->

permalink: /:categories/:title/
---

```
syntax enable
set background=dark
colorscheme solarized
execute pathogen#infect()
autocmd VimEnter * NERDTree
autocmd VimEnter * wincmd p
```

> https://github.com/tpope/vim-pathogen
> https://github.com/scrooloose/nerdtree
