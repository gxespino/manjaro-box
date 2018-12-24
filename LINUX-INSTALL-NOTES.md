Things to install/script away eventually:

# POST INSTALL

- Add Read/Write access to user
`sudo chown -R glenn /usr/share`

- Configure Pacman mirrors to use local mirrors
`sudo pacman-mirrors -g && sudo pacman -Syyu`

- Remove palemoon browser
`sudo pacman -Qsq '^palemoon' | sudo pacman -R -`

- Disable Continuous trim for SSD Drives
`sudo systemctl enable fstrim.timer`

- Install package managers
`sudo pacman -Sy yaourt`

- Update system and all packages
`sudo pacman -Syyu; yaourt -Syua && yaourt -Syua --devel --needed`

- Install Codecs and multimedia plugins (for videos/music)
`sudo pacman -S exfat-utils fuse-exfat a52dec faac faad2 flac jasper lame libdca libdv gst-libav libmad libmpeg2 libtheora libvorbis libxv wavpack x264 xvidcore flashplugin libdvdcss libdvdread libdvdnav dvd+rw-tools dvdauthor dvgrab`

- Install Printer essentials
`sudo pacman -S lib32-libcups cups gutenprint libpaper foomatic-db-engine ghostscript gsfonts foomatic-db cups-pdf system-config-printer`
`sudo systemctl enable org.cups.cupsd.service`
`sudo systemctl enable cups-browsed.service`
`sudo systemctl start  org.cups.cupsd.service`
`sudo systemctl start  cups-browsed.service`

- Disable PC Speaker
`sudo rmmod pcspkr`
`echo 'blacklist pcspkr' > /etc/modprobe.d/nobeep.conf`

- Disable memory card reader for less power consumption during sleep
`echo "2-3" | sudo tee /sys/bus/usb/drivers/usb/unbind`
 Also do this in BIOS

- Enable Thunderbolt BIOS Assist mode for less power consuption during sleep - In BIOS

- Install Lenovo Throttling Fix
`yay -S lenovo-throttling-fix-git`
`sudo systemctl enable --now lenovo_fix.service`

- Update grub
`sudo update-grub`

# PERSONAL APPS

- Install Chromium
`sudo pacman -Syu`
`sudo pacman -S Chromium`

- Set Chromium as default browser
`xdg-settings set default-web-browser chromium.desktop`
`echo '$BROWSER=/usr/bin/chromium' >> $HOME/.profile`

- Spotify
`yaourt -S spotify`

# DOTFILES

- Remap CAPS LOCK
`touch .Xmodmap`
`echo 'clear lock' >> $HOME/.Xmodmap`
`echo 'keysym Caps_Lock = Control_L' >> $HOME/.Xmodmap`

- Add alias for deep clean
`sudo pacman -Scc; sudo paccache -rk0; sudo pacman -Rsn $(pacman -Qdtq); history -c; history -w`

...TODO...
1. Set chromium i3 shortcut to Super + c
2. Add vim shortcuts
3. Optimize startup
4. Optimze power
5. Configure sleep
6. Alias sleep commands and add to dotfiles

- Add asdf
`git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.6.2`
`echo -e '\n. $HOME/.asdf/asdf.sh' >> ~/.bashrc`
`echo -e '\n. $HOME/.asdf/completions/asdf.bash' >> ~/.bashrc`

- Install basic Ruby/Rails dev env
`asdf plugin-add ruby`
`asdf install ruby 2.5.0`
`asdf global ruby 2.5.0`
`gem install bundler`
`gem install rake`
