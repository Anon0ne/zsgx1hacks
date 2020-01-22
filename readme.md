 # NOTICE: YOU MUST MAKE A BACKUP OF YOUR HOME DIRECTORY FOR NEWER INQMEGA PTZ381 CAMERAS!
NOTICE: You MUST make a backup of your Home directory for the newer INQMEGA PTZ 381 cameras, especially devParam.dat once your WiFi connection is setup. This is a work in progress, and the camera has deleted my devParam.dat multiple times requiring me to connect via ethernet and restore it. I am still trying to figure out this camera, the root directory is not writable; and the root filesystem is full. We have to figure out how to get the SD Card to load these files via mount each time we reboot, but the stock hack does not work it will brick the camera.
I will list the steps I used to modify my INQMEGA PTZ381, the hack loaded to SD Card will not work directly.

Get your camera setup with the YCC365 app first, and then we will remove the cloud connection settings, and backup our home directory. We can have the SD card loaded to make changes whenever we wish.
Load the files in the repo to your SD Card, then load the SD card into the camera and reboot it. Use whatever method you need to (checking router, wireshark, etc to find your local IP address of camera) and connect via telnet.

First, mount the card to media to make it simple.
  mount -t vfat /dev/mmcblk0p1  /media

Let's begin to update busybox
  mkdir -p /home/busybox
  mount --bind /media/busybox /bin/busybox
  /bin/busybox --install -s /home/busybox

If you need SSH, you can run the below command.
 # setup and install dropbear ssh server - no password login
  /media/dropbearmulti dropbear -r /media/dropbear_ecdsa_host_key -B
  
If you need FTP, you run the below command.
 # start ftp server
  (/home/busybox/tcpsvd -E 0.0.0.0 21 /home/busybox/ftpd -w / ) &
  
Now, you can switch to SSH or stay on the Telnet.

 # change directory
   cd /home
   
 Once in your home directory, type "ls" and you will see a list of files; I did a copy/paste of devParam.dat, cloud.ini, cloud_oversea.ini and them remove the cloud files.
 
 cp devParam.dat devParam.dat.bak
 cp cloud.ini cloud.ini.bak
 cp cloud_oversea.ini cloud_oversea.ini.bak
 
  # set RTSP password & etc
  
Now, lets edit the hwcfg.ini file and setup an RTSP password!

  vi hwcfg.ini
  
  Hit CTRL + I to go into editing mode, and go to the bottom of the list and make a new line with enter. You will type the below command
  
  passwd = yourpassword
  
If you want to remove the PTZ test you can do that as well with the below command.
 
 do_test = 0
 
  Now hit the ESC key on your keyboard, and then type :wq followed by ENTER key to save and exit.
  
type "reboot" in the command line, and your cam should now have new settings applied. I also blocked the domains in the cloud.ini file via my Pi-Hole and pFSense for added security. Atleast we have access to the camera now. Will update this stuff as i figure out how to make this automatic.

# INQMEGA PTZ381 1080p Camera Streams
Verified working in VLC & iSpy Viewer

rtsp://admin:yourpassword@your-ip:554 (If you setup a password in the hwcfg.ini, use this with yourpassword changed)

rtsp://admin@your-ip:554/0/av0 (This one works for some programs, although others require you to remove the admin@ portion.)

rtsp://your-ip:8001 (This will require a login if you have it setup. I believe this is a lower quality/resolution stream)

http://your-ip:554/snapshot (This is a still-image snapshot of the stream)


# Still in progress
Other Ports to investigate:

PORT     STATE SERVICE
23/tcp   open  telnet
80/tcp   open  http
554/tcp  open  rtsp
843/tcp  open  unknown
5050/tcp open  mmcc
7103/tcp open  unknown
8001/tcp open  vcom-tunnel
