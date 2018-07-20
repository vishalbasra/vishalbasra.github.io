---
layout: post
title:  "Quirky VIM things"
categories: Vim

categories:
  - Vim

tags:
  - Vim
  - Editor
  - Macos


excerpt: Vim on macos!
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
