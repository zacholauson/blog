---
layout: post
title: "Resetting TSlime settings"
date: 2014-06-05 22:57

---
TSlime is great for my testing workflow, but if I add/remove a pane my settings get screwed up, which means I have to restart vim to reset the settings.

Turns out its actually pretty simple to reset the TSlime settings.

Here is what I setup:

{% codeblock .vimrc lang:vim %}
map <leader>Tn :call ResetTSlimePaneNumber()<CR>
map <leader>Ts :call ResetTSlimeVars()<CR>

function! ResetTSlimeVars()
  execute "normal \<Plug>SetTmuxVars"
endfunction

function! ResetTSlimePaneNumber()
  let g:tslime['pane'] = input("pane number: ", "", "custom,Tmux_Pane_Numbers")
endfunction
{% endcodeblock %}

With this you can use `\<Leader>Tn` to just reset the pane number, which is usually what I need to do,
or `\<Leader>Ts` to reset all of the TSlime settings.
