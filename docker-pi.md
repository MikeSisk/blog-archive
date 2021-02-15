---
title: "Installing Docker on a Raspberry Pi"
date: 2019-11-09
draft: false
---

The Raspberry Pi is a fantastic low-cost way to experiment with Docker and 
Kubernetes.

There’s several ways to get the latest version of Docker installed on a 
Raspberry Pi; you can go with the Cypriot project or a full-fledged 
GUI-based Raspbian installation.

In my case I have a cluster of six Pi I need to configure for a Kubernetes 
install and I want the latest “Buster” release so I’m going with Raspbian 
Buster Lite.

After downloading the ISO image you need to burn it to the MicroSD card. On 
my modern Apple MacBook Pro this isn’t as easy as it used to be since there’s 
on SD card slot and all you get is USB-C ports. So, yeah, dongle life it is.

I used this Uni brand card adapter and it works perfectly.

There’s all kinds of ways to burn the Raspbian ISO file to the MicroSD card, 
but I prefer the command-line way.

First, insert the MicroSD card into the reader and do this to see which 
“disk” device it’s assigned to:

```
mike@jurassic ~ % diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *500.3 GB   disk0
   1:                        EFI EFI                     314.6 MB   disk0s1
   2:                 Apple_APFS Container disk1         500.0 GB   disk0s2

/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +500.0 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume Jurassic - Data         161.0 GB   disk1s1
   2:                APFS Volume Preboot                 87.4 MB    disk1s2
   3:                APFS Volume Recovery                528.5 MB   disk1s3
   4:                APFS Volume VM                      1.1 GB     disk1s4
   5:                APFS Volume Jurassic                10.8 GB    disk1s5

/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *63.9 GB    disk2
   1:             Windows_FAT_32 boot                    268.4 MB   disk2s1
   2:                      Linux                         63.6 GB    disk2s2

mike@jurassic ~ % 
```

As you can see, in this case the 64GB MicroSD card I’m using is mounted as 
/dev/disk2. To write to it we need to unmount it. This is different than 
“ejecting”; we need to umount it but still leave it attached to the system 
so we can write to it.

```
mike@jurassic ~ % diskutil umountDisk /dev/disk2
Unmount of all volumes on disk2 was successful
mike@jurassic ~ % 
```

Now we’re ready to write the downloaded Raspbian image to the MicroSD card.

```
sudo dd bs=1m if=/Users/mike/Downloads/2019-09-26-raspbian-buster-lite.img of=/dev/rdisk2 conv=sync
```

This will take awhile to run and after it completes the image will 
automatically mount to the system. Leave it this way for the next step.

I run my Raspberry Pi cluster headless, so instead of hooking up a monitor and 
keyboard to each Pi to configure it there’s several features you can enable 
on the newly imaged MicroSD card to streamline it’s headless setup.

First we need to enable ssh so we can login remotely. You can do this by 
creating an empty file named ‘ssh’ on the /boot partition.

Next we need to configure networking. I network my cluster via Wifi so we can 
create a file called wpa_supplicant.conf also on the /boot filesystem and when 
the Pi boots it’ll copy this file and it’s contents to the correct location 
and fire up the network.

```
mike@jurassic ~ % cd /Volumes/boot 
mike@jurassic boot % touch ssh
mike@jurassic boot % touch wpa_supplicant.conf
mike@jurassic boot % vi wpa_supplicant.conf 
```

Here’s the contents I use in wpa_supplicant.conf to attach to my wifi. Change 
the attributes to match your wifi settings.

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=US

network={
    ssid="Cenozoic"
    key_mgmt=WPA-PSK
    psk="trilobite"
}
```

Ok, now we’re ready to eject the MicroSD card from the Mac and put it in the 
Pi and power it up.

```
mike@jurassic ~ % diskutil eject /dev/disk2
Disk /dev/disk2 ejected
```

After you power up the Pi you should be able to login to it via ssh. Of 
course, you’ll need to find out it’s IP address. There’s several ways you 
can do this with a wifi headless setting. You can just check your routers 
MAC table or DHCP settings if you have access. Or yu can use nmap to scan 
your network and find a list of devices with active ssh ports.

Once you figure that out and ssh in you’re ready to configure your Pi with 
Docker.

Login as the ‘pi’ user and change the default password to something more 
secure. I usually like update the OS, too.

```
passwd
sudo apt update 
sudo apt full-upgrade -y
sudo reboot
```

After the reboot with an updated system it’s time to get docker installed.

First, we need to install some packages we need to install docker:

```
sudo apt-get install apt-transport-https ca-certificates software-properties-common -y
```

Next we need to download and install the docker gpg key:

```
sudo wget -qO - https://download.docker.com/linux/raspbian/gpg | sudo apt-key add -
```

We need to setup the apt channel to fetch the docker pacakges:

```
sudo echo "deb https://download.docker.com/linux/raspbian/ buster stable" | sudo tee /etc/apt/sources.list.d/docker.list
```

Now, we’re ready to install Docker. The --no-install-recommends is to work 
around an issue with aufs-dkms you might run into. It’s not needed for the 
latest versions of Docker so we can skip it and avoid the issue.

```
sudo apt-get update
sudo apt-get install docker-ce --no-install-recommends -y
```

The last step for setup is to enable the pi user to run docker commands:

```
sudo usermod -aG docker pi
```

Logout of the pi account and back in and you should be good to go. Run docker 
info to check that Docker is running and is the latest version.

&#x269B;
