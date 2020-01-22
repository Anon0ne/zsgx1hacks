NOTICE: You MUST make a backup of your Home directory for the newer INQMEGA PTZ 381 cameras. 
I will list the steps I used to modify my INQMEGA PTZ 381, the hack loaded to SD Card will not work directly.

Get your camera setup with the YCC365 app first, and then we will remove the cloud connection settings, and backup our home directory. We can have the SD card loaded to make changes whenever we wish.
Load the files in the repo to your SD Card, then load the SD card into the camera and reboot it. Use whatever method you need to (checking router, wireshark, etc to find your local IP address of camera)

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
