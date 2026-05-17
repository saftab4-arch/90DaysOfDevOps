## Linux Boot Flow

Power On  
→ BIOS / UEFI  
→ Bootloader (GRUB)  
→ Linux Kernel Loads  
→ systemd starts (PID 1)  
→ Services & Processes Start

Example services:
- Docker
- SSH
- Kubernetes components
- Networking services

---

# Core Components of Linux

## 1. Kernel
The kernel is the core of Linux.

Responsibilities:
- Memory management
- CPU scheduling
- Process management
- Device communication
- File system management

Command:
```bash
uname -r

![Kernel Version](screenshots/uname-r.png) 


2. User Space

User space is where users and applications run.

Examples:

Bash shell
Docker CLI
Terminal applications
Editors like nano/vim

3. systemd (PID 1)

systemd is the first process started by the kernel.

Responsibilities:

Starts services during boot
Restarts failed services
Manages background processes
Handles logs and service states

Useful command:

systemctl status ssh

![systemd PID1](screenshots/systemd-pid1.png) 

4: Linux File System Basics

Everything in Linux starts from /

Examples:

Directory	Purpose
/home	User files
/etc	Configuration files
/bin	Basic commands
/sbin	System binaries
/proc	Process information
/sys	System information
/boot	Boot files
/mnt	Mounted storage

![OS Release](screenshots/os-release.png)

5: Process Management

Everything in Linux runs as a process.

Process States - State	Meaning
Running -	Currently using CPU
Sleeping -	Waiting for task/input
Stopped	- Paused process
Zombie	- Finished but not cleaned up

![ps aux](screenshots/ps-aux.png) 

![top](screenshots/top.png)

![htop](screenshots/htop.png)

![systemctl](screenshots/systemctl-ssh.png)



5 Linux Commands I Will Use Daily
1. pwd

Shows current directory

![pwd](screenshots/pwd.png)

2. ls -l

Lists files and directories

3. cd

Changes directory

4. df -h

Shows disk usage

![df-h](screenshots/df-h.png)

5. free -h

Shows memory usage

![free-h](screenshots/free-h.png)

6. ip a

![ip a](screenshots/ip-a.png)

Additional Useful Commands



mkdir test
touch file.txt
cat file.txt
history
uptime
date
nohup
wc -l

Head and Tail Commands

First line of file
head file.txt -n 1

![head](screenshots/head.png)


Last line of file
tail file.txt -n 1

![tail](screenshots/tail.png)

Help Commands
man mkdir
![man mkdir](screenshots/man-mkdir.png)

mkdir --help
![mkdir help](screenshots/mkdir-help.png)

Why This Matters for DevOps

Understanding Linux internals helps with:

Troubleshooting crashed services
Debugging CPU/memory issues
Understanding logs
Managing production servers
Restarting services confidently

Linux is the foundation of modern cloud and DevOps environments.


# Docker Commands Used

## Create Container

```bash
docker run -itd --name linux-lab ubuntu bash
```

## Login into Container

```bash
docker exec -it linux-lab bash
```

## Delete Container

```bash
exit
docker rm -f linux-lab




