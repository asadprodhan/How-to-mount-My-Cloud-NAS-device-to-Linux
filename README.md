# **How to mount the My Cloud NAS device to the Linux computer** <br />


My Cloud NAS device is a digital data storage device. NAS stands for Network Attached Storage. As the name suggests, My Cloud NAS device can be connected to a network and thus accessed remotely. However, the NAS device first needs to be included (i.e. mounted) in the computer file system to access it. This is in contrast with the other data storage devices such as USB flash drives or Solid-State Drives (SSDs), which are automatically mounted in the file system when they are physically connected to a computer.
<br />
<br />
Here, I present the stepwise commands to mount a password protected My Cloud NAS device to a Linux (Ubuntu 18) computer.
<br />
<br />
<br />
## **Requirement**


The My Cloud NAS device and the Linux computer need to be connected to the same network.
<br />
<br />
## **How to mount?** 


**Let’s collect the IP addresses that are required to perform the mounting.**
<br />
<br />
<br />
**I.	Find the IP address of the network**  


```
ifconfig
```


>‘ifconfig’ stands for ‘interface configuration’. It is a Linux command that reveals the ID of the network that is in operation. 
>After running ifconfig, look out for wlan0 if you are using WiFi network, or eth0 or eno1 for ethernet network. And collect the ‘inet (internet) address’. 


>**It will be something like ‘inet addr:192.168.1.29’.** 


An IP address consists of four numbers separated by a period (.). The first three numbers are the network ID, and the fourth number is a device ID (also called ‘host ID’). In the above example, 192.168.1 is the network ID and 29 is one of the devices connected to the network, 192.168.1. Every hardware connected to the internet is given a unique identifier. 
<br />
<br />
<br />
**II.	Find the IP address of the My Cloud NAS device.** 

To do so, we need scan the network. ‘nmap’ (Network Mapping) is an open-source network scanner that discovers the devices connected to a given network. 


```
sudo apt-get install nmap 
```

Then


```
sudo nmap -sn 192.168.1.0/24
```


This will show the IP addresses of the devices that are connected to the network ID 192.168.1. Note that the first three numbers are the network ID. ‘0/24’ indicates that the host IDs can range from 0 to 256 (this is a conversion from binary to decimal numbers. In brief, the whole IP address can be upto 32 bits, 8 for each number. '0/24' in the place of the host ID indicates that 24 bits have already been used in the network ID, and thus the host ID can have upto 8 bits, which is 0 to 256 in decimal number).
<br />
<br />
<br />
**Now, we have the IP address for our NAS device- say, 10.65.37.96. Let’s mount it to the Linux computer.**
<br />
<br />
<br />
**III.	We need a Linux tool called CIFS (Common Internet File System)**


```
sudo apt install cifs-utils
```


**IV.	Then, create a directory where the NAS device will be mounted. This directory is called a mount-point. It can be at /media or /mnt directory**


```
sudo mkdir /media/NAS
```


>Here, NAS is a directory under ‘media’, where we will mount our NAS device.



**V.	As our NAS device has a username and password, we need to create a file that will contain these credentials. During the mounting process, we will need to supply this credential file.**


```
nano ~/.smbcredentials
```


Enter the credentials:


```
username=peterNAS
password=lx5690nas
```


Close the file. We need to make this file accessible by changing the permission. However, no other users can read this file as it is in your home directory. 


```
 sudo chmod 600 ~/.smbcredentials
 ```
 
 
**VI.	Now, we need to enter the following details in /etc/fstab file. This is a file that contains the mounting details (configurations) for any network devices**
 
 
 ```
sudo nano /etc/fstab
```


Enter the following details:


```
//<server_name>/<share_name>  /media/<share_name>  cifs  uid=<user_name>,credentials=/home/<user_name>/.smbcredentials,iocharset=utf8 0 0
```


An example:


- The NAS device IP address:  10.65.37.96


- The NAS directory that we want to mount to the computer: data


- The Mount-point that we have created in the Linux computer: /media/NAS


- The uid (user ID, this is the Ubuntu user name)= peter


- /.smbcredentials= We have created this file above. It contains our NAS device credentials



**So, our /etc/fstab entry will look like as follows:**


```
//10.65.37.96/data /media/NAS cifs uid=peter,credentials=/home/peter/.smbcredentials,iocharset=utf8 0 0
```


Save and exit the fstab file. **My Cloud NAS device has been mounted.** Check the mounting by the following command


```
sudo mount -a
```


‘mount -a’ instructs the computer to mount all the devices entered into the /etc/fstab file. No error means successful completion of the mounting process. 
Finally, run the ‘mount’ command and you should be able to see all the mounted devices including the My Could NAS device in your terminal.


```
mount
```

