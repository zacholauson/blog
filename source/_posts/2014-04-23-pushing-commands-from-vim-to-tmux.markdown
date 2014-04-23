---
layout: post
title: "Pushing commands from vim to tmux"
date: 2014-04-23 10:32

---
Yesterday I was working on a Rails app and I was trying to figure out the best way to speed up my feedback with RSpec. I had been using [vim-rspec](https://github.com/thoughtbot/vim-rspec) but in a Rails app with a decent sized test suite, I dont really want to be pulled out of my vim session to run a few seconds worth of tests.
Vim RSpec allows you to define a custom command to to specify how rspec gets run when you use their bindings, so combining that with another excellent vim plugin [tslime.vim](https://github.com/jgdavey/tslime.vim), I can tell vim-rspec to push my rspec command to a different tmux pane and or window.

Add plugins to vim (this is using vundle):
{% codeblock lang:vim .vimrc.bundles %}
Bundle 'thoughtbot/vim-rspec'
Bundle 'jgdavey/tslime.vim'

{% endcodeblock %}

Run `vim +:BundleInstall` to install plugins


Add vim-rspec config using tslime to vimrc:
{% codeblock lang:vim .vimrc %}

let g:rspec_command = 'call Send_to_Tmux("bundle exec rspec {spec}\n")'

map <Leader>t :call RunCurrentSpecFile()<CR>
map <Leader>s :call RunNearestSpec()<CR>
map <Leader>l :call RunLastSpec()<CR>
map <Leader>a :call RunAllSpecs()<CR>

{% endcodeblock %}

When you run a vim-rspec command the first time it will prompt you for your window name and pane number if you have more than one pane. -- You can see what a pane's number is if you do `prefix-key + q` in tmux.
