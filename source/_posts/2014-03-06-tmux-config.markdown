---
layout: post
title: "Tmux Config"
date: 2014-03-06 16:40

---
This morning I played around with my Tmux config for a while. I didnt add a whole lot to it today, but over all I'm pretty happy with my config.

```
unbind C-b
set -g prefix C-a
```

This is probably my most important config. When I was first learning how to use Tmux I found a great post by Thoughtbot about shortcuts and I started learning with this shortcut and I really like it. It simply changes your tmux "leader" key to `<ctrl> a`.

```
setw -g mode-keys vi

bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R
```

These options set your movement keybindings to be like vim, so `ctrl+a hjkl` for movement. You can also set this to act like emacs.

Tmux has excellent man pages, so learning shortcuts is pretty easy.

```
set -g mode-mouse on
set -g mouse-resize-pane on
```

Im normally pretty against any mouse mode settings, but I like this one for resizing my panes quickly.

* * *

The rest of my config is pretty basic so I wont explain each piece and I have simple comments explaining some pieces.

## Some of my favorite shortcuts

```
ctrl+a % - vertical split
ctrl+a " - horizontal split

ctrl+a { - swap current pane with pane to the left
ctrl+a } - swap current pane with pane to the right

ctrl+a [ - copy mode

ctrl+a , - rename window
ctrl+a $ - rename session

ctrl+a : - opens command line at the bottom for tmux commands

ctrl+a c - new window
```

## Full config

```
set -g default-terminal 'screen-256color'
set-option -g default-command "reattach-to-user-namespace -l zsh"
set -g history-limit 100000

# act like GNU screen
unbind C-b
set -g prefix C-a

# act like vim
setw -g mode-keys vi

# Switch panes with vim directions
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# Go back a window or forward a window
bind-key -r C-h select-window -t :-
bind-key -r C-l select-window -t :+

# switch to last pane
bind-key C-a last-pane

# mouse mode on for resizing panes
set -g mode-mouse on
set -g mouse-resize-pane on

# Copy into mac clipboard
unbind -t vi-copy Enter
bind-key -t vi-copy v begin-selection
bind-key -t vi-copy y copy-pipe "reattach-to-user-namespace pbcopy"

# start window numbers at 1 to match keyboard order with tmux window order
set -g base-index 1
set -g renumber-windows on

# pane border
set-option -g pane-border-fg colour240
set-option -g pane-active-border-fg colour246

# Status Bar
set-option -g status-bg colour236
set-option -g status-fg colour203

set-window-option -g window-status-current-fg colour253
set-window-option -g window-status-current-bg default
set-window-option -g window-status-current-attr bright

set-option -g status-utf8 on
set-option -g status-justify "left"
set-option -g status-left-length 60
set-option -g status-right-length 90

set -g status-position bottom
set -g status-left "#S"
set -g status-right "#(~/bin/battery_status)%% | #[fg=colour203]%R"

# window labels
setw -g window-status-format "[ #I: #W ]"
setw -g window-status-current-format "[ #I: #W ]"
```
