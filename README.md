# Ubuntu-20.04-LXC-Desktop
Ubuntu LXC Desktop for Proxmox

Ubuntu proxmox LXC Container with desktop

Update: Container for proxmox installed available
https://drive.google.com/file/d/1iwZ9FETJVwcplVrBoqL9v444J82kMGJh/view?usp=sharing

root password: F6h(W*Tsps=De?+k

admin password:  F6h(W*Ts

Part A: setting the template
1) create a template for ubuntu, give it 100G of storage, 2 or 4 cpu (can change to what you want), specify the ip you want (ex: 192.168.1.10/24), provide a gateway.
2) once you start the new lxc. Log in as root and edit Source.list:

sudo nano /etc/apt/sources.list

Add the following sources:
deb http://archive.ubuntu.com/ubuntu/ focal main restricted universe multiverse

deb-src http://archive.ubuntu.com/ubuntu/ focal main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu/ focal-updates main restricted universe multiverse

deb-src http://archive.ubuntu.com/ubuntu/ focal-updates main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse

deb-src http://archive.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse

deb http://archive.ubuntu.com/ubuntu/ focal-backports main restricted universe multiverse

deb-src http://archive.ubuntu.com/ubuntu/ focal-backports main restricted universe multiverse

deb http://archive.canonical.com/ubuntu focal partner

deb-src http://archive.canonical.com/ubuntu focal partner

3) Install the desktop environment: during the install you will see a screen asking which desktop you want : select lighdm.
sudo apt install xfce4 xfce4-goodies xorg dbus-x11 x11-xserver-utils

4) since we want to access the lxc through remote RDP, install XRDP
sudo apt install xrdp

5) Verify xrdp installed correctly and is running:
sudo systemctl status xrdp

6) once RDP is installed we need to add this code, so that desktop manager know what to use for display:
update-alternatives --set x-session-manager /usr/bin/xfce4-session

7) install Firefox browser or any other software you like to have in your templates (Terminal/chrome browser, for example)
sudo apt-get install firefox -y

8) crate a backup of the new created LXC as as GZIP. first, turn off the lxc container and then do the backup from Proxmox GUI.
Pve-> select container <id> —> Backup now—> compression select GZIP (good)

9) once the backup is completed. Go the Shell of Proxmox: copy the new file created to the folder where Proxmox saves templates and give it a new name.In my case the templates are saved in the following folder, your will be different, look under your zpool or where you saved your selected to save templates when you download them:Template-folder: /your-zfs-pool-name/zfs-iso-template/template/cacheBackup-folder-file: /your-zfs-pool/zfs-backups/dump/vzdump-lxc-111-2020_07_27-14_52_18.tar.gz
cp backup-folder-file template-folder/new-name.tar.gz

10) That's it! Now when you go to create new container, you will have a new template that you can use to create new containers with desktop environment and all software per-installed.

Part B: getting the sound to work:

1) get latest download link of xrd-installer-1.2 from this website:
cd opt
wget http://c-nergy.be/downloads/xRDP/xrdp-installer-1.2.zip

unzip xrdp*

chmod +x xrdp-installer-1.2.sh

2) create a new user if you don't already have one:
adduser admin

usermod -aG sudo admin

su admin

./xrdp-installer-1.2.sh -s

3) reboot container.
 reboot

4) After rebooting the container, i was getting Dummy output in the Volume control and no sound output. To fix the issue restart pulseaudio server in the container by running the following command:
pulseaudio -k

After pulseaudio is restarted, the sound should start to work with xrdp sink appearing in the volume control.

Part C:
You can use/try a different desktop environment than xfce4, after all this is the beauty of LXC. before turning the new container to a template try different desktop environments. once you make your choice, remove/delete old desktop then set container as template in order to keep it to a small size.
for UBUNTU-MATE desktop use the following:
sudo apt install lightdm ubuntu-mate-core ubuntu-mate-desktop -y

sudo update-alternatives --set x-session-manager /usr/bin/mate-session


References/Credits:
I want to give credits to the following developers and their sites. i wouldn't have came up with the steps without the help of information they provided.
https://github.com/neutrinolabs/xrdp
https://linuxize.com/post/how-to-install-xrdp-on-ubuntu-18-04/
https://github.com/bmullan/ciab-remote-desktop/blob/master/mk-cn1-environment.sh
https://linuxconfig.org/8-best-ubuntu-desktop-environments-18-04-bionic-beaver-linux
http://c-nergy.be/blog/?p=14888
https://www.closingtags.com/custom-lxc-container-templates-in-proxmox-5-0/
https://blog.simos.info/how-to-run-...pps-in-lxd-containers-on-your-ubuntu-desktop/
