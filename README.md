# Pop OS - Setup Guide
When I started to install Pop OS on my dell XPS, I found it difficult to get the right combination of settings in the BIOS 
to move away from the preinstalled Windows OS. After making the necessary changes, I was able to install Pop OS in UEFI mode without issues.
Next I made a list of my setup based on my own research and by taking pieces from others' guides online. A lot of good ideas on this
site https://mutschler.eu/linux/install-guides/pop-os-post-install/ and the lush-pop guide in github.

## Install Pop OS
### Download Pop OS
https://pop.system76.com/

It is always a good idea to check the hash once the file downloads
* On Windows - Open CMD or PowerShell `certutil -hashfile [name_of_file.iso] sha256`
* On Linux - `sha256sum [name_of_file.iso]`

### Create USB bootable drive
My favorite program for creting bootable drives is [balena Etcher](https://www.balena.io/etcher/)

### Adjust BIOS settings
These settings are appropriate for the DELL XPS but should also help with other systems
1. Plug in the USB
1. Reboot your system
1. Press F2 repeatedly on startup to enter BIOS
    1. Secure Boot: Disable _(This is needed for NVIDIA drivers, and also if you want to use virtual machines)_
    1. Enable Legacy Option ROMS _(Find this inside Settings > General > Advanced Boot Option)_
    1. SATA Operation: AHCI _(my system was on RAID mode by default, which is not supported by Linux. 
Making this change has the potential to remove the windows boot, 
so there are steps one must follow in Windows before making this change dual-boot is desired)_
1. Save and Exit
1. Press F12 repeatedly on statrup to enter the Boot options menu
1. Select the USB option with the UEFI / EFI details

_as long as the BIOS settings were chosen correctly, the system drive will be available for installation. 
Otherwise it will not be listed in the possible install locations and you will have to re-check the BIOS settings_

## Basic Housekeeping
### Update and Upgrade
After initially setting up the primary user, open a terminal to run the commands  
`sudo apt-get update`  
`sudo apt-get upgrade`  
`sudo apt dist-upgrade`  
`sudo apt autoremove`  
`sudo apt autoclean`  
`sudo fwupdmgr get-devices`  
`sudo fwupdmgr get-updates`  
`sudo fwupdmgr update`  
`sudo reboot now`  

### Graphics Adapters
For machines with NVIDIA GPUs using Pop_OS you can run these commands to switch to hybrid and only use NVIDIA card when necessary
More information on [switching graphics](https://support.system76.com/articles/graphics-switch-pop/)  
`sudo system76-power graphics hybrid`  
`sudo reboot now`

### Enable Firewall
Pop OS (and many others) come with ufw installed. It is configured through CLI. But since this is a Pop installation
it is more likely that the user would prefer a GUI interface  
`sudo apt-get install gufw`  
Open gufw and enable via the GUI

### Gparted
For some reason Disks has replaced GParted, which is more intuative  
`sudo apt install -y gparted`

### MS Fonts
`sudo apt install -y ttf-mscorefonts-installer`

### Nautilus
Admin - _Right-Click context menu to use in Naitulus (Files) such as admin rights_  
`sudo apt install -y nautilus-admin`  
Dropbox - _Nautilus connector_  
`sudo apt install -y nautilus-dropbox`  
*Run dropbox from the applications menu*

### Stacer
Great troubleshooting system monitor  
`sudo apt install -y stacer`

### Install Timeshift
Timeshift is used to take snapshots. This can be used to revert a bad setting.  
Timeshift puts all snapshots into /run/timeshift/backup.  
`sudo apt install timeshift`  
Now that this is installed, open it
- Select RSYNC
- Select a backup drive (in order of preference)
    - External
    - Secondary
    - Same drive
- Include all files
- Create a backup

### VLC and Multimedia Codecs
`sudo apt install -y vlc`  
`sudo apt install -y libavcodec-extra libdvd-pkg; sudo dpkg-reconfigure libdvd-pkg`

### Visual Studio Code
`sudo apt install -y code`

### Flatpaks 
Install through PopShop  
GitKraken - _Best Git repo UI_  
Zoom - _Video Conferencing_  
Steam  
Discord  
Slack  
Spotify  


### Gnome Extensions
Install the native host messaging application  
`sudo apt install chrome-gnome-shell`  
  
Install the browser extension  
These are installed from https://extensions.gnome.org
- Caffine - *Disable the screensaver and auto suspend*
- User Themes
- Removable Drive Menu
- Bluetooth Quick Connect
- Dash to Dock

### Go through all programs
Hit META+A and go through all programs, decide whether you need them or uninstall these.

## GUI Changes
### Terminal Transparency
Preferences > Pop > Colors  
- [x] Use transparent background
- [ ] Use transparency from system theme

### Gnome Tweaks (Basic)
`sudo apt install gnome-tweaks`  

**Settings to Change**
- Change Time to AM/PM
- Add Day
- Close lid does nothing
- Turn off bluetooth
- Change wallpaper
- Automatically delete recent files and trash
- Turn of screen after 15 min
- Turn on night mode
- Deactivate system sounds, mute mic
- Turn of suspend, shutdown for power button
- Go through keyboard shortcuts and adapt, I also add a custom one for xkill on CTRL+ALT+X
- Check region and language, remove unnecessary languages, then update

### Gnome Look
- Gnome Shell Themes: Flat Remix GNOME
- GTK3 Themes: Prof-Gnome-theme

### Desktop Clock and Weather (Conky)
1. Install Conky  
`sudo apt install conky-all curl jq gawk`
2. Grab the minimal clock widget from the repository  
`cd Downloads`  
`git clone https://github.com/VaughnValle/lush-pop.git`
3. Create a free account in OpenWeatherMap
Get your free API key in the API section of the website  
Search the City ID of your location here  
Install the Poiret One font in **$DIR/lush-pop/Poiret**  
4. Install MNML Conky  
*NOTE: Leave your answers blank when prompted during installation by hitting Enter*
`cd $DIR/lush-pop/mnml_conky && ./install`
5. Copy conky configuration file to your home directory  
`cp .conkyrc ~/.conkyrc`
6. Edit the conky configuration file  
`nano ~/.conkyrc`  
- Place your API key in the template6 variable
- Place your City ID in the template7 variable
7. Run conky in the terminal  
`conky`
8. Add conky to your startup apps

## Future Possible Changes
### Virtual Machines
look into [QEMO](https://github.com/wmutschl/quickemu)
### Backups
look into Nextcloud server for docker box
### Remote
look into a VNC viewer (such as TigerVNC) to remote into other linux DEs  
Use this if need to use RDP for a windows machine  
`sudo apt install -y rdesktop`  
`echo "rdesktop -g 1680x900 wiwi-farm.uni-muenster.de -r disk:home=/home/$USER/  &" > ~/wiwi.sh`  
`chmod +x wiwi.sh`  
`cat <<EOF > ~/.local/share/applications/wiwi.desktop`  
`[Desktop Entry]`  
`Name=WIWI Terminal Server`  
`Comment=WIWI Terminal Server wiwi-farm`  
`Keywords=WIWI;RDP;`  
`Exec=/home/$USER/wiwi.sh`  
`Icon=preferences-desktop-remote-desktop`  
`Terminal=false`  
`MimeType=application/x-remote-connection;x-scheme-handler/vnc;`  
`Type=Application`  
`StartupNotify=true`  
`Categories=Network;RemoteAccess;`  
`EOF`
