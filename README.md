# Read This
You can see my buildscript repository for scripts which can help you a lot.

This is the patch to fix the `out of memory heap error`.
It occurs mainly during the compilation of Metalava.
To fix this error run these commands in the source of the rom [xxx@linux:/home/customrom/lineageos]
  
    cd build/soong && git fetch https://github.com/nparashar150/android_build_soong && git cherry-pick c8ba7af59acda55a16835727d1d351b8d58a5ca4
    
Now after cherry picking you need other patches and configurations.

If you want to autoconfigure zram copy paste this

    sudo apt install zram-config
    
Then after this type this command and just put a # in front of swap if u have a swap partition or if you have a swap file you can just ignore it

    sudo nano /etc/fstab

Installing ZRAM
    Open the terminal create the zram.conf file 
    
    sudo nano /etc/modules-load.d/zram.conf
        
   Paste this inside the file zram.conf and save and exit 
        
    zram
    
   Create another new file 
    
    sudo nano /etc/modprobe.d/zram.conf
       
   Paste this inside the file zram.conf and save and exit
    
    options zram num_devices=1
        
   For configuring the size of the ZRAM create new file
        
    sudo nano /etc/udev/rules.d/99-zram.rules
        
   Paste this inside the file and currently I will suggest the size to be 8Gib but you can increase if you want.
        
    KERNEL=="zram0", ATTR{disksize}="8192M",TAG+="systemd"
        

After this the ZRAM is created but its better if you disable traditional swap
    Open the terminal and edit the fstab file and put a `#` in the line of swap.
      
    sudo nano /etc/fstab
      
    # swap was on /dev/sda7 during installation
    #UUID=5ad493cf-6f4a-42ab-9b41-7f6bf9167b3e none            swap    sw              0>
 
   Something like that will be the output.

In order to run ZRAM you need to create a system unit file.

    sudo nano /etc/systemd/system/zram.service
    
Add these lines inside the file.

    [Unit]
    Description=Swap with zram
    After=multi-user.target

    [Service]
    Type=oneshot 
    RemainAfterExit=true
    ExecStartPre=/sbin/mkswap /dev/zram0
    ExecStart=/sbin/swapon /dev/zram0
    ExecStop=/sbin/swapoff /dev/zram0

    [Install]
    WantedBy=multi-user.target
    
Save and close it.

To enable the ZRAM run this

    sudo systemctl enable zram

Reboot your system and ZRAM is created xD.

Now to check if ZRAM is working run this.

    cat /proc/swaps
    
And you will see zram like this

    /dev/zram0                              partition	8388604	538860	-2
    
Now dont forget to restar your computer    

Add this to your build command so that Metalava compiles first

    . build/envsetup.sh && lunch <codename>_<devicename>-userdebug && make api-stubs-docs && m/make bacon

Good Luck I hope the Android R build you are trying to build compiles now. 



[Thank You](https://github.com/gigabyte-1000)
