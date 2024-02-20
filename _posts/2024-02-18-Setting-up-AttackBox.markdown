---
layout: post
title:  "Setting up AttackBox"
date:   2024-02-18 12:50:01 -0600
categories: Hackersploit
image: /assets/img/hackersploit.jpg
---
# Hackersploit Attack Station setup
[Youtube Link](https://www.youtube.com/watch?v=v-PObOuJMsQ&t=2013s)

## Install Terminator
```
sudo add-apt-repository ppa:gnome-terminator
sudo apt-get -y install terminator
terminator
```
## Install zsh (already installed on THM)
```
sudo apt install zsh
zsh --version
```
## Install Oh-My-Zsh Plugin
```
sudo apt install -y git-core curl fonts-powerline 
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" 
```
## oh-my-zsh pentest plugin
```
git clone https://github.com/jhwohlgemuth/zsh-pentest.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-pentest
```
## oh-my-zsh quiver plugin
```
git clone https://github.com/stevemcilwain/quiver.git ~/.oh-my-zsh/custom/plugins/quiver

```

# Update your zsh config
```
sudo nano ~/.zshrc
ZSH_THEME="mh"
plugins=(git zsh-pentest nmap quiver web-search) 

# Example aliases
alias zshconfig="nano ~/.zshrc"
alias ohmyzsh="nano ~/.oh-my-zsh"
alias serve="python3 -m http.server 9999"
```

restart zsh and ready to go!
