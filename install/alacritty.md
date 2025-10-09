# Alacritty Installation & Configuration Guide (Linux Mint)

### Introduction

Alacritty is a GPU-accelerated terminal emulator that is lightweight, fast, and highly configurable.
This guide covers how to:

Install Alacritty on Linux Mint

Configure JetBrains Mono Nerd Font for icons

Manage configurations using GNU Stow

#### Prerequisites

Linux Mint 21.x or Ubuntu 22.04+

GNU Stow installed for dotfiles management

Homebrew (optional, for consistency across systems)

JetBrainsMono Nerd Font installed

### 1.Install font JetBrains Mono Nerd font

```bash
mkdir -p ~/.local/share/fonts

wget -O ~/.local/share/fonts/JetBrainsMono.zip \
  https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip

unzip ~/.local/share/fonts/JetBrainsMono.zip -d ~/.local/share/fonts/JetBrainsMono
fc-cache -fv

```

**Check**

```bash
fc-list | grep "Nerd"

```

### 2.Install Alacritty

#### Official PPA method

```bash

sudo apt install software-properties-common -y
sudo add-apt-repository ppa:aslatter/ppa -y
sudo apt update
sudo apt install alacritty -y

```

**Check**

```bash
alacritty --version

```

### 3.Configure Font & Appearance

Create a directory in dotfiles

```bash
mkdir ~/dotfiles/alacritty/

```

Then create config file

```bash

nvim ~/dotfiles/alacritty/alacritty.toml

```

Configuration

```toml

[env]
TERM = "xterm-256color"

[window]
padding.x = 10
padding.y = 10

# decorations = "Buttonless"
decorations = "None" #for linux also

opacity = 0.85
blur = true

option_as_alt = "Both"

[font]
size = 12.0

[font.normal]
family = "JetBrainsMono Nerd Font"
style = "Regular"

[font.bold]
family = "JetBrainsMono Nerd Font"
style = "Bold"

[font.italic]
family = "JetBrainsMono Nerd Font"
style = "Italic"

[font.bold_italic]
family = "JetBrainsMono Nerd Font"
style = "Bold Italic"



[keyboard]
bindings = [
{ key = "N", mods = "Control|Shift", action = "CreateNewWindow" },
{ key = "F", mods = "Control|Shift", action = "ToggleFullscreen" },
{ key = "L", mods = "Control|Shift", action = "ToggleMaximized" },
]
```

### Using GNU Stow

Make symlink

```bash
cd ~/dotfiles
stow alacritty

```

**Check**

```bash
ls -l ~/.config | grep alacritty

```
