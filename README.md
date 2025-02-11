# Table of Content:

1.  [Linux Cheat Sheet for Cloud & Security: Directories, Commands & Best Practices](#linuxcheatsheet)

----------------------------------

# Linux Cheat Sheet for Cloud & Security: Directories, Commands & Best Practices <a name="linuxcheatsheet)"></a> 

## Moving Around (Navigation Basics)

### Here are the basic commands to move through the Linux file system:

`pwd` Prints the current working directory (where you are).     
`ls` Lists files and directories in the current location.   
`cd <directory>` Change into a directory. Example: `cd /home/user/Documents`  
`cd ..` Move one level up (back out of a directory).    
`cd -` Switch to the previous directory you were in.    
`cd ~` Move to the home directory of the current user.  
`cd /` Move to the root directory (/).  

## Helpful Shortcuts & Tips
* Tab Completion: Press `Tab` while typing a directory or file name to auto-complete it.   
* Using `ls -l` or `ls -la`  Shows detailed directory listings, including hidden files (. files).    
* Grep for Files: Use `grep` to search inside files:    
    Example: `grep "error" /var/log/syslog` (Finds occurrences of "error" in the system logs).

## Online Cheat Sheets resources for Bash
Here are some useful Bash cheat sheets to reference:    
* [Cheat Sheet for Bash Commands](https://github.com/RehanSaeed/Bash-Cheat-Sheet?utm_source=chatgpt.com)
* [Linux Command Cheat sheet](https://www.geeksforgeeks.org/linux-commands-cheat-sheet/?utm_source=chatgpt.com)
* [Bash Scripting Cheat Sheet](https://devhints.io/bash?utm_source=chatgpt.com)
* [Manual Page (man page) Simplified with Practical Examples ](https://tldr.sh/)

## Understanding Linux Root Directories for Security & Cloud Engineers
For **Cloud Engineers**, understanding the Linux file system is crucial when managing cloud-based instances. Knowing where configurations, logs, and binaries are located helps with troubleshooting, automation, and server management.

For **Security Engineers**, knowing the root directory structure is essential for monitoring anomalies, hardening system security, and analyzing forensic data. Attackers often target specific directories to manipulate logs, escalate privileges, or persist malware.

## Why it matters to Security & Cloud Engineers

|Directory| Purpose| Why It Matters (Cloud & Security) |
| ------ | ------ | ----- |
| /bin | Essential binary executables (e.g., ls, cat, cp). | If an attacker replaces one of these tools with a fake version, they can trick you into running their malicious code instead. [Path Hijacking](https://www.youtube.com/watch?v=IPs5xz5aEzk) explained |
|/boot | Stores important files that help your system start up, including: The **boot loader** (like GRUB), which tells your machine how to start. The **[Linux kernel](https://youtu.be/5S-tTDeFZfY?si=qcWUOA0oiY4HoN9L)**, the core part of the operating system. | **Cloud Engineers** may need to tweak GRUB (the boot loader) to optimize system performance. **Security Engineers** must lock down /boot because attackers could replace the files in the kernel with a malicious version ([rootkits](https://youtu.be/X9x2UzzXP8w?si=e7W-sCkHovi0HBy-)), gaining full control of the system.
|/dev| Contains device files, which represent hardware and virtual devices like: Hard drives (`/dev/sda`, `/dev/nvme0n1`) or USB devices (`/dev/usb1`) or Network interfaces (`/dev/net/tun`) Instead of interacting directly with hardware, Linux treats everything as a file, making it easier to manage. | Attackers might create fake devices to exfiltrate data. Monitor for unexpected devices. |
|/etc| System-wide configuration files (e.g., `passwd`, `ssh/sshd_config`).| Stores system-wide configuration files that control how Linux behaves. Think of it as the "settings folder" for your system | Security Engineers must harden configurations to prevent unauthorized access.|
| /home |  User directories (`/home/user`). Stores personal files.|  Think of it like your "Documents” folder | If a user's account is compromised, their files might be exfiltrated. Consider disk encryption and strict permissions.
|/lib| Contains shared system libraries that programs and system binaries need to function. Think of it like Windows DLL (Dynamic Link Libraries) files—these are essential for running commands and applications. | **Cloud Engineers** must maintain library integrity to prevent server failures and security risks. **Security Engineers** monitor `/lib` for unauthorized changes to block malware and rootkits. | 
| /media| A directory where the system mounts (attaches) external storage devices such as USB drives, CDs, and DVDs.| Cloud instances typically don't need to support external drives, so enabling auto-mounting can be a security risk.| 
| /mnt | A directory used as a temporary location to manually mount filesystems, such as network drives or external storage. |Unexpected mounts could indicate an attempt to bypass security controls. |
| /opt | Used for installing optional, third-party software that is not managed by the system’s default package manager (like `apt` or `yum`). | Security Engineers must monitor for unauthorized software installations that could be backdoors or trojans.| 
| /proc| Provides real-time information about system processes, hardware, and kernel settings.| Attackers can abuse it to hide malicious activity, so monitoring and access control are critical.|
| /root | Root user’s home directory (not the same as `/`).|  If compromised, full system control is lost. Restrict SSH access and enforce MFA. If an attacker gains access, they control the entire system. | 
| /sbin | Stores essential system management commands (`shutdown`, `fdisk`). |  Attackers might try to disable system services or modify partition tables. Monitor for unexpected execution.|
| /srv |  Holds data for services like web servers and databases |Cloud Engineers might store web content, databases, or API data. Security Engineers should monitor for unauthorized file changes.|
|/tmp |  A storage area for temporary files, cleared on reboot. | Often targeted for malware and  privilege escalation. | 
|/usr |  User binaries, libraries, and shared files (`/usr/bin`).|  If modified, attackers might replace binaries to create backdoors. Monitor for unauthorized file changes.| 
| /var| Stores logs, mail, and cached files that change frequently. |  Security Engineers must monitor log files for anomalies, intrusion attempts, or log tampering. | 

