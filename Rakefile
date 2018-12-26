require 'tmpdir'

def step(description)
  description = "-- #{description} "
  description = description.ljust(80, '-')
  puts
  puts "\e[32m#{description}\e[0m"
end

def app_path(name)
  path = "/Applications/#{name}.app"
  ["~#{path}", path].each do |full_path|
    return full_path if File.directory?(full_path)
  end

  return nil
end

def app?(name)
  return !app_path(name).nil?
end

def link_file(original_filename, symlink_filename)
  original_path = File.expand_path(original_filename)
  symlink_path = File.expand_path(symlink_filename)
  if File.exists?(symlink_path)
    # Symlink already configured properly. Leave it alone.
    return if File.symlink?(symlink_path) and File.readlink(symlink_path) == original_path
    # Never move user's files without creating backups first
    number = 1
    loop do
      backup_path = "#{symlink_path}.bak"
      if number > 1
        backup_path = "#{backup_path}#{number}"
      end
      if File.exists?(backup_path)
        number += 1
        next
      end
      mv symlink_path, backup_path, :verbose => true
      break
    end
  end
  ln_s original_path, symlink_path, :verbose => true
end

def unlink_file(original_filename, symlink_filename)
  original_path = File.expand_path(original_filename)
  symlink_path = File.expand_path(symlink_filename)
  if File.symlink?(symlink_path)
    symlink_points_to_path = File.readlink(symlink_path)
    if symlink_points_to_path == original_path
      # the symlink is installed, so we should uninstall it
      rm_f symlink_path, :verbose => true
      backups = Dir["#{symlink_path}*.bak"]
      case backups.size
      when 0
        # nothing to do
      when 1
        mv backups.first, symlink_path, :verbose => true
      else
        $stderr.puts "found #{backups.size} backups for #{symlink_path}, please restore the one you want."
      end
    else
      $stderr.puts "#{symlink_path} does not point to #{original_path}, skipping."
    end
  else
    $stderr.puts "#{symlink_path} is not a symlink, skipping."
  end
end


namespace :install do
  # LINUX CONFIGURATIONS

  desc 'Add read/write access to user'
  task :read_write_access do
    step 'Add read/write access to user'
    sh 'sudo chown -R $USER /usr/share'
  end

  desc 'Configure Pacman mirrors to use local mirrors'
  task :configure_pacman do
    step 'Configure Pacman mirrors to use local mirrors'
    sh 'sudo pacman-mirrors -g --noconfirm && sudo pacman -Syyu'
  end

  desc 'Remove palemoon browser'
  task :remove_palemoon do
    step 'Remove palemoon browser'
    sh "sudo pacman --noconfirm -Qsq '^palemoon' | sudo pacman -R -"
  end

  desc 'Disable continuous trim for SSD drives'
  task :disable_cont_trim do
    step 'Disable continuous trim for SSD drives'
    sh 'sudo systemctl enable fstrim.timer'
  end

  desc 'Install arch package manager Yay'
  task :yay do
    step 'Install arch package manager Yay'
    sh 'sudo pacman -S --noconfirm yay'
  end

  desc 'Update system and all packages'
  task :updates do
    step 'Update system and all packages'
    sh 'sudo pacman -Syyu; yay -Syua && yay -Syua --devel --needed'
  end

  desc 'Install codecs and multimedia plugins for videos/music'
  task :codecs do
    step 'Install codecs and multimedia plugins for videos/music'
    sh 'sudo pacman -S --noconfirm exfat-utils fuse-exfat a52dec faac faad2 flac jasper lame libdca libdv gst-libav libmad libmpeg2 libtheora libvorbis libxv wavpack x264 xvidcore flashplugin libdvdcss libdvdread libdvdnav dvd+rw-tools dvdauthor dvgrab'
  end

  desc 'Install printer essentials'
  task :printer_essentials do
    step 'Install printer essentials'
    sh 'sudo pacman -S --noconfirm lib32-libcups cups gutenprint libpaper foomatic-db-engine ghostscript gsfonts foomatic-db cups-pdf system-config-printer'
    sh 'sudo systemctl enable org.cups.cupsd.service'
    sh 'sudo systemctl enable cups-browsed.service'
    sh 'sudo systemctl start  org.cups.cupsd.service'
    sh 'sudo systemctl start  cups-browsed.service'
  end

  desc 'Enable Lenovo throttling fix'
  task :lenovo_throttling_fix do
    step 'Enable Lenovo throttling fix'
    sh 'yay -S lenovo-throttling-fix-git'
    sh 'sudo systemctl enable --now lenovo_fix.service'
  end

  desc 'Update grub'
  task :grub do
    step 'Update grub'
    sh 'sudo update-grub'
  end

  desc 'Remap CAPS_LOCK'
  task :remap_caps_lock do
    step 'Remap CAPS_LOCK'
    sh 'touch .Xmodmap'
    sh "echo 'clear lock' >> $HOME/.Xmodmap"
    sh "echo 'keysym Caps_Lock = Control_L' >> $HOME/.Xmodmap"
  end

  # PERSONAL APPS

  desc 'Install Chromium'
  task :chromium do
    step 'Install Chromium'
    sh 'sudo pacman -Syu && sudo pacman -S chromium'
  end

  desc 'Set Chromium as default browser'
  task :default_browser do
    step 'Set Chromium as default browser'
    sh 'sudo xdg-settings set default-web-browser chromium.desktop' 
    sh "echo '$BROWSER=/usr/bin/chromium' >> $HOME/.profile"
  end

  desc 'Install Spotify'
  task :spotify do
    step 'Install Spotify'
    sh 'yay -S --noconfirm spotify'
  end

  # EDITOR/DOTFILES

  desc 'Install terminator terminal'
  task :terminator do
    step 'Install terminator terminal'
    sh 'sudo pacman -Syyu terminator'
  end

  desc 'Install Vim'
  task :vim do
    step 'vim'
    sh 'sudo pacman -Syyu vim'
  end

  desc 'Install tmux'
  task :tmux do
    step 'tmux'
    sh 'sudo pacman -Syyu tmux'
  end
end

namespace :disable do
  desc 'Disable PC Speaker'
  task :pc_speaker do
    step 'Disable PC Speaker'
    sh 'sudo rmmod pcspkr'
    sh "sudo echo 'blacklist pcspkr' > /etc/modprobe.d/nobeep.conf"
  end

  desc 'Disable memory card reader for less power consumption during sleep'
  task :mem_card do
    step 'Disable memory card reader for less power consumption during sleep'
    sh "sudo echo '2-3' | sudo tee /sys/bus/usb/drivers/usb/unbind"
  end
end

DOTFILES = [
    '.aliases',
    '.bash_profile',
    '.bash_prompt',
    '.bashrc',
    '.exports',
    '.gitconfig',
    '.tmux.conf',
    '.vimrc',
    '.zshrc'
]

desc 'Install these config files.'
task :default do
  Rake::Task['install:read_write_access'].invoke
  # Rake::Task['install:configure_pacman'].invoke
  # Rake::Task['install:remove_palemoon'].invoke
  # Rake::Task['install:disable_cont_trim'].invoke
  # Rake::Task['install:yay'].invoke
  # Rake::Task['install:updates'].invoke
  # Rake::Task['install:codecs'].invoke
  # Rake::Task['install:printer_essentials'].invoke
  # Rake::Task['install:lenovo_throttling_fix'].invoke
  # Rake::Task['install:grub'].invoke
  # Rake::Task['install:remap_caps_lock'].invoke
  # Rake::Task['install:chromium'].invoke
  # Rake::Task['install:default_browser'].invoke
  # Rake::Task['install:spotify'].invoke
  # Rake::Task['install:terminator'].invoke
  # Rake::Task['install:vim'].invoke
  # Rake::Task['install:tmux'].invoke

  # Rake::Task['disable:pc_speaker'].invoke
  # Rake::Task['disable:mem_card'].invoke
  
  step 'git submodules'
  sh 'git submodule update --init'

  step 'symlink dotfiles'

  DOTFILES.each do |file|
    link_file "#{file}", "~/manjaro-box/dotfiles/#{file}"
  end

  step 'symlink i3 config'
  link_file 'i3/config', '~/.config/i3/config'
end

desc 'Uninstall these config files.'
task :uninstall do
  step 'un-symlink'

  # un-symlink files that still point to the installed locations
  DOTFILES.each do |file|
    unlink_file "#{file}", "~/#{file}"
  end

  unlink_file 'i3/config', '~/.config/i3/config'
end
