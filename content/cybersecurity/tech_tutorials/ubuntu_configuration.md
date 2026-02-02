---
title: "Ubuntu Configuration"
draft: false
description: "Ubuntu를 메인OS로 사용할때의 환경설정"
tags: ["english"]
categories: ["configuration"]
date: 2026-02-02 09:00:00+0900
---

## System
- Region & Language : hangul
	- Settings -> Region & Language -> Language Support -> Install / Remove Languages : check Korean
	- reboot
	- append input method(hangul)
		```bash
		# open ibus preference windows
		ibus-setup
		```
		input method -> add -> korean -> hangul
	- Settings -> Keyboard -> Input Sources -> Add Input Source -> Korean -> Korean (Hangul)
		remove English (us) or previous input source when you selected during installation time.
- Swap Ctrl <-> Caps Lock
	```bash
	sudo apt install gnome-tweak-tool
	```
	Tweaks -> Keyboard -> Additional Layout Options -> Swap Ctrl and Caps Lock
## Apps
- Terminator : Terminal
	```bash
	sudo apt install terminator
	```
- Obsidian : Editor
- Visual Studio Code : Editor
- Configuration/rclone : file sync and mount
- VMware Workstation
	```bash
	chmod a+x VMware-Workstation-Full-17.6.4-24832109.x86_64.bundle
	sudo ./VMware-Workstation-Full-17.6.4-24832109.x86_64.bundle
	```
	실행하면 추가 모듈 설치중 에러가 발생
	```bash
	sudo vmware-modconfig --console --install-all
	```
- gcc
	```bash
	sudo apt install build-essential
	```
- git
	```bash
	# installation
	sudo apt install git
	
	# config
	git config --global user.name "seul"
	git config --global user.email "seul@seul.dev"
	git config --global core.precomposeunicode true
	git config --global core.quotepath false
	```
- oh-my-zsh
	```bash
	sudo apt install zsh
	sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
	# zsh-syntax-highlighting
	git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
	# zsh-autosuggestions
	git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
	```
- powerlevel10k
	```bash
	git clone --depth=1 https://github.com/romkatv/powerlevel10k.git "${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k"
	```
	edit ~/.zshrc
	```plaintext
	plugins=(
		git
		sudo
		colored-man-pages
		zsh-syntax-highlighting
		zsh-autosuggestions
	)
	```
- neovim
	```bash
	sudo apt install ninja-build gettext cmake curl build-essential git
	git clone https://github.com/neovim/neovim
	cd neovim
	make CMAKE_BUILD_TYPE=RelWithDebInfo
	cd build && cpack -G DEB && sudo dpkg -i nvim-linux-x86_64.deb
	sudo apt install python3-neovim
	```
	edit ~/.zshrc, append config
	```plaintext
	alias vim="nvim"
	alias vi="nvim"
	alias vimdiff="nvim -d"
	export EDITOR=/usr/bin/nvim
	```
- python3
	append config into ~/.zshrc
	```plaintext
	alias python=python3
	alias pip=pip3
	```
- 7zip
	```bash
	sudo apt install p7zip
	```
- Tactile : split screen
	```bash
	sudo apt intsall gnome-shell-extension-manager
	# after install the manager, launch it.
	# browse tactile & install it.
	```
- VLC : media player
	```bash
	sudo apt install vlc
	```
- Telegram
	```bash
	sudo snap install telegram-desktop
	```
- Signal
	```bash
	sudo snap install signal-desktop
	```
- NFS
	```bash
	sudo apt instal nfs-common
	```
- Flatpak
	```bash
	sudo apt install flatpak
	sudo apt install gnome-software-plugin-flatpak
	flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
	# restart the system
	```
- Sticky Notes
	```bash
	# installation
	flatpak install flathub com.vixalien.sticky
	# (run) flatpak run com.vixalien.sticky
	```
	**autorun**
	vi ~/.config/autostart/sticky.desktop
	```plaintext
	[Desktop Entry]
	Type=Application
	Exec=flatpak run com.vixalien.sticky
	Hidden=false
	NoDisplay=false
	X-GNOME-Autostart-enabled=true
	Name=Sticky Notes
	Comment=자동 실행 Sticky Notes
	```
	reboot
- Libre Office
	```bash
	flatpak install flathub org.libreoffice.LibreOffice
	```
- Font 설치
	```bash
	# mv fonts into ~/.local/share/fonts
	mkdir -p ~/.local/share/fonts
	mv *.ttf ~/.local/share/fonts
	fc-cache fv
	```