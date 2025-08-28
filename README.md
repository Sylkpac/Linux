# Table of Content:

1.  [Linux Cheat Sheet for Cloud & Security: Directories, Commands & Best Practices](#linuxcheatsheet)
2.  [Understanding File Permissions in Linux (Bash)](#filelinux)
3.  [tmux: A Powerful Multi-Pane Terminal](#tmux)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# Linux Cheat Sheet for Cloud & Security: Directories, Commands & Best Practices<a name="linuxcheatsheet"></a> 

## Moving Around (Navigation Basics)

### Here are the basic commands to move through the Linux file system:

`pwd` Prints the current working directory (where you are).     
`ls` Lists files and directories in the current location.   
`cd <directory>/` Change into a directory. Example: `cd home/user/Documents/`  
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


------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


# Understanding File Permissions in Linux (Bash) <a name="filelinux"></a> 

## Breaking Down File Permissions
When you list files in Linux using `ls -l`, you’ll see something like this:     
`-rw-r--r--. 1 ec2-user ec2-user 16 Jan  8 18:09 class_notes.txt`

## Let’s break it down (left to right):

|Section| Example| Meaning |
|------| -------| --------- | 
| File Type| `-` |A dash (-) means it's a file. If it were a directory, it would be d.|
|Owner Permissions | `rw-` | The owner (ec2-user) has read (r) and write (w) permissions but no execute (x) permission.|
|Group Permissions| `r--` | The group (ec2-user) can only read (r) the file.| 
| Other Permissions| `r--`| Everyone else can read (r) the file but not write (w) or execute (x).|
| Links|`1`|Number of hard links to the file.|
|Owner & Group| `ec2-user ec2-user`|The file is owned by user ec2-user and belongs to the group ec2-user.|
| Size| `16` |File size in bytes.
|Last Modified Date| `Jan 8 18:09` | Last time the file was modified.|
|File Name| `class_notes.txt`| Name of the file.|


## What About Directories?
Directories have different permissions. Here's an example:

`drwxr-xr-x. 2 ec2-user ec2-user 30 Jan  8 19:09 class_lessons`    

`d`  It’s a directory.      
`rwx`  The owner can read (r), write (w), and execute (x) (which means they can enter the directory).       
`r-x` The group can read (r) and execute (x), but not write.    
`r-x`  Others can read (r) and execute (x), but not write.

## How to Change File Permissions (chmod)
The `chmod` command is used to change permissions of files and directories.


### Using Symbolic Mode (Letters)
You can modify permissions using letters like `r`, `w`, and `x`.   

**Common chmod Examples:**      
`chmod u+x myscript.sh` Give the file owner execute permission  
`chmod g-w myfile.txt` Remove write permission from the group       
`chmod o+r myfile.txt` Allow others to read the file        
`chmod u+rwx myfile.txt` Give the owner full permissions (read, write, execute)

|Symbol| Meaning| 
|------|--------|
|`u`| User (owner)|
|`g`|Group|
|`o`| Others (everyone else)
|`a`| All (user, group, others)
|`+`| Add permission
|`-`|Remove permission
|`=`| Set exact permissions


### Using Numeric Mode (Numbers)      
Instead of `rwx`, you can use numbers:        

|Number| Permission | Binary Representation |
|-------|----------| -----------|
|`0` | No permissions |`---` (000)
|`1` |Execute only |`--x` (001)
|`2`| Write only|`-w-` (010)
|`3`| Write & Execute |`-wx` (011)
|`4`| Read only |`r--` (100)
|`5`| Read & Execute |`r-x` (101)
|`6`| Read & Write |`rw-` (110)
|`7`|Read, Write & Execute| `rwx` (111)

### Common chmod Numeric Examples:

`chmod 755 script.sh` Owner: `rwx`, Group: `r-x`, Others: `r-x`     
`chmod 644 document.txt` Owner: `rw-`, Group: `r--`, Others: `r--`      
`chmod 700 private_file` Owner: `rwx`, Group: `---`, Others: `---`      
`chmod 777 public_file` Everyone can read, write, execute (not recommended for security)


## Bash Cheat Sheet for File Permissions

|Command| Description| 
|-------|------------|
|`ls -l`|List files with permissions
|`chmod 755 <filename>`|Set rwxr-xr-x permissions using numbers
|`chmod u+x <filename>`|Give owner execute permission
|`chmod -R 700 <foldername>` | Recursively set permissions for a directory
|`chown user:group <filename>`|Change file owner and group
|`umask 022`|Default permission mask (affects new files)


### Why File Permissions Matter for Security
Prevents unauthorized access (e.g., sensitive files should not be readable by everyone).
Protects against malware execution (e.g., restricting execute permissions can prevent unwanted scripts from running).
Helps enforce least privilege (users only get access to what they need).

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## tmux: A Powerful Multi-Pane Terminal <a name="tmux"></a> 

**Description:** Tmux is a multi-pane terminal that allows you to manage multiple terminal sessions within a single window. It's a valuable tool for cybersecurity professionals due to its features such as detaching and reattaching sessions, creating multiple windows and panes, enhanced security, increased productivity, and collaboration capabilities.

### Installation:

* **Linux:** Use your package manager (e.g., sudo apt install tmux)

* **macOS:** Use Homebrew (brew install tmux)
  
* **Windows:** Use Windows Linux Subsytem (WSL) 

### Getting Started:

* Launch tmux: 'tmux'
* Navigate between panes and windows using arrow keys and 'Ctrl+b' combinations.
* Create new windows and panes using 'Ctrl+b' followed by appropriate commands.
* Commands found here: https://tmuxcheatsheet.com/

### Key Features:

* <b>Detach and reattach sessions</b>   
  - Detach from a session: 'Ctrl+b' then 'd'     
  - List sessions: 'tmux ls'     
  - Reattach to a session: 'tmux attach-session -t 0' (assuming session 0 is listed).

* **Create multiple windows and panes**
  - Start a new tmux session: 'tmux'
  - Create a new window for a different task: Press 'Ctrl + b' then 'c'
  - In the new window, split the screen horizontally to run two commands simultaneously: Press 'Ctrl + b' then '%'
  - Now split the first pane vertically to create even more workspace: Press 'Ctrl + b' then '"'
  - Navigate between the panes and windows using:   
      Next Window: 'Ctrl + b' then 'n'   
      Previous Window: 'Ctrl + b' then 'p'   
      Pane Navigation: 'Ctrl + b' then use arrow keys
      
* Enhanced security (session locking, password protection)
  - To lock your tmux session, use the following key combination: 'Ctrl + b' then 'x'  
*This will lock the session and prompt for a password to unlock it
  - To unlock a locked session, simply press 'Enter' and you will be prompted to enter your password.
  - To enhance security, set a lock command by executing: ' tmux set-option -g lock-command "gnome-screensaver-command -l" '
  - Press 'Ctrl + b' then 'l' to lock the terminal. You will need to enter your password again to unlock.

* Change layouts and shortcuts
  
  - Split the Pane Horizontally: Press 'Ctrl + b' then '%' to split the current pane horizontally.
  - Split the Pane Vertically: Press 'Ctrl + b' then ' " ' to split the current pane vertically.
  - Change the Layout: Press 'Ctrl + b' then 'space' to cycle through layouts until you find one you like.
 
* Kill server/ kill everything
  - From Outside the tmux Session: If you're not currently in a tmux session, you can run the following command in your terminal: 'tmux kill-server'
    *This command kills all active tmux sessions and terminates tmux entirely.

### Additional Resources:

* Video Tutorial:      
https://youtu.be/nTqu6w2wc68?si=R1VCBMUYTXQxhlp9   
https://www.youtube.com/watch?v=DzNmUNvnB04&t   
* Refer to the tmux manual for more detailed instructions:  
https://github.com/tmux/tmux/wiki/

