---
layout: post
title: "Faster Control P"
date: 2014-06-01 17:15

---

Control P is one of my favorite / most used vim plugins, but I did notice it lagging a bit when Im working in a big project.

If we change Control P to use Ag instead of Grep, we can speed it up a lot.

First we need to install Ag

{% codeblock lang:sh %}
brew install the_silver_searcher
{% endcodeblock %}

Optional: Install ag.vim, awesome replacement for using grep to find things in vim

{% codeblock .vimrc lang:vim %}
Bundle 'rking/ag.vim'
{% endcodeblock %}

Finally, change user command for Control P to use Ag

{% codeblock .vimrc lang:vim %}
let g:ctrlp_user_command = 'ag %s -f -l --nocolor -g ""'
{% endcodeblock %}
