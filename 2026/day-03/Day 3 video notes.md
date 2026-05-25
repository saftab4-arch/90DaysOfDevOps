# Day 03 – Linux Commands Practice: Extra Lab Notes

This file contains my extra Day 03 Linux practice notes, commands, examples, and troubleshooting reminders.

## Lab Goal

Build confidence with common Linux commands used in real troubleshooting:

- SSH and key-based authentication
- User and group management
- File permissions and ownership
- Package management
- Service/process management
- Hard links and soft links
- System resource checks
- `awk` and command filtering

---

## 1. SSH Key-Based Login Practice

### Lab Setup

Create two Linux servers:

```text
I-am-server-a
I-am-server-b
```

### Goal

Connect from **Server A** into **Server B** using SSH public/private key authentication.

### Steps Practiced

On **Server B**, generate an SSH key pair:

```bash
ssh-keygen
```

This creates:

```text
id_rsa        # private key
id_rsa.pub    # public key
authorized_keys
```

Add the public key into the `authorized_keys` file on Server B:

```bash
cat id_rsa.pub >> ~/.ssh/authorized_keys
```

Copy the private key to Server A and save it as:

```text
server-b-key.pem
```

From **Server A**, connect to Server B:

```bash
ssh -i server-b-key.pem ubuntu@<server-b-public-ip>
```

### Key Concept

SSH key authentication uses asymmetric encryption:

- Public key stays on the server you want to access.
- Private key stays with the client connecting in.
- After first successful connection, the host gets stored in `known_hosts`.
- Next time, SSH usually does not ask to verify the host again.

---

## 2. Sudoers File

The sudoers file controls which users can run commands with elevated privileges.

```bash
sudo visudo
```

Important idea:

```text
sudoers = permissions for who can run what as root/admin
```

Never directly edit sudoers with a normal editor unless you know exactly what you are doing. `visudo` checks syntax before saving.

---

## 3. User Management

Each Linux user normally gets a home directory inside `/home`.

Example:

```text
/home/ubuntu
/home/hamza
/home/basit
```

### Switch User

```bash
su hamza
```

Check current user:

```bash
whoami
```

---

## 4. Create Users with `useradd`

Create user and home directory:

```bash
sudo useradd -m hamza
```

Set password separately:

```bash
sudo passwd hamza
```

### Important Flags

```bash
-m
```

Creates a home directory:

```text
/home/hamza
```

Create user with home directory and bash shell:

```bash
sudo useradd -m -s /bin/bash hamza
sudo passwd hamza
```

Create user with home directory, bash shell, and sudo group:

```bash
sudo useradd -m -s /bin/bash -G sudo hamza
sudo passwd hamza
```

---

## 5. Create Users with `adduser`

Alternative command:

```bash
sudo adduser hamza
```

`adduser` automatically:

- Creates the home directory
- Copies default files from `/etc/skel`
- Sets the default shell
- Creates a matching primary group
- Prompts for password
- Asks optional profile questions
- Sets correct home directory permissions

### Difference Between `useradd` and `adduser`

| Command | Behavior |
|---|---|
| `useradd` | Lower-level command, needs flags like `-m` |
| `adduser` | More user-friendly, interactive, creates home automatically |

---

## 6. Delete Users

Delete a user:

```bash
sudo userdel hamza
```

Delete a user and their home directory:

```bash
sudo userdel -r hamza
```

---

## 7. Group Management

### Add User to Sudo Group

```bash
sudo usermod -aG sudo hamza
sudo usermod -aG sudo basit
```

Command format:

```bash
sudo usermod -aG <group-name> <username>
```

Example:

```bash
sudo usermod -aG devops hamza
sudo usermod -aG tester anton
sudo usermod -aG devops basit
```

### Verify Sudo Group Members

```bash
getent group sudo
```

### Add User to Group with `gpasswd`

```bash
sudo gpasswd -a hamza tester
```

### Remove User from Group

```bash
sudo gpasswd -d hamza tester
```

### Check All Groups

```bash
cat /etc/group
```

### Check All Users

```bash
cat /etc/passwd
```

### Check Password/Shadow File

```bash
sudo cat /etc/shadow
```

Important: `/etc/shadow` contains protected password hash information and should only be viewed with proper admin permission.

---

## 8. File Permission Basics

Example output:

```bash
-rw-r--r-- 1 root root 0 May 25 19:00 hello.txt
```

Breakdown:

```text
-           regular file
rw-         owner permissions
r--         group permissions
r--         others permissions
root        owner
root        group
hello.txt   file name
```

### File Type Indicator

| Symbol | Meaning |
|---|---|
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |

### Permission Numbers

| Permission | Number |
|---|---:|
| Read | 4 |
| Write | 2 |
| Execute | 1 |

Examples:

```bash
chmod 600 hello.txt
chmod 644 hello.txt
chmod 755 script.sh
```

Meaning:

```text
600 = owner can read/write, group and others have no access
644 = owner can read/write, group and others can read
755 = owner can read/write/execute, group and others can read/execute
```

---

## 9. Change File Ownership with `chown`

`chown` changes the owner of a file.

Example:

```bash
sudo chown ubuntu:ubuntu hello.txt
```

Format:

```bash
sudo chown <user>:<group> <filename>
```

Example changing owner and group:

```bash
sudo chown hamza:devops hello.txt
```

Another example:

```bash
touch nice.txt
sudo chown basit:tester nice.txt
```

Verify:

```bash
ls -l
```

---

## 10. Package Management

Package managers by operating system:

| OS | Package Manager |
|---|---|
| Ubuntu/Debian | `apt` / `apt-get` |
| CentOS | `yum` |
| Red Hat | `rpm` / `yum` / `dnf` |
| Amazon Linux | `yum` or `dnf` |
| macOS | `brew` |

### Ubuntu Package Commands

Update package index:

```bash
sudo apt-get update
```

Install Nginx:

```bash
sudo apt-get install nginx
```

Remove package:

```bash
sudo apt remove nginx
```

Remove package and related config files:

```bash
sudo apt purge nginx
```

Check Nginx version:

```bash
nginx -v
```

---

## 11. Service Management with `systemctl`

Check SSH service status:

```bash
systemctl status ssh
```

Check Nginx status:

```bash
systemctl status nginx
```

Start Nginx:

```bash
sudo systemctl start nginx
```

Stop Nginx:

```bash
sudo systemctl stop nginx
```

Restart Nginx:

```bash
sudo systemctl restart nginx
```

Enable Nginx on boot:

```bash
sudo systemctl enable nginx
```

Disable Nginx on boot:

```bash
sudo systemctl disable nginx
```

---

## 12. Service Management with `service`

Older/simple service command examples:

```bash
sudo service nginx start
sudo service nginx stop
sudo service nginx status
```

### Key Note

Both `systemctl` and `service` can manage services, but modern Linux systems usually use `systemctl` because it talks directly to `systemd`.

---

## 13. Process Lock Troubleshooting

Check which process has locks:

```bash
sudo lslocks
```

Kill a stuck process by PID:

```bash
sudo kill -9 <PID>
```

Example:

```bash
sudo kill -9 1234
```

Important: `kill -9` force kills a process. Use it carefully.

---

## 14. Hard Links

Create a file:

```bash
cd /home/ubuntu
touch hi.txt
nano hi.txt
```

Create a hard link:

```bash
ln /home/ubuntu/hi.txt /home/hello-file
```

Read from hard link:

```bash
cat /home/hello-file
```

### Hard Link Concept

A hard link points to the same inode/data on disk.

If the original file name is deleted, the hard link still keeps the data as long as at least one hard link remains.

---

## 15. Soft Links / Symbolic Links

Create a symbolic link:

```bash
ln -s /home/ubuntu/devops/hello.txt /home/hello-file-s
```

Read the symbolic link:

```bash
cat /home/hello-file-s
```

Delete the symbolic link only:

```bash
rm /home/hello-file-s
```

### Soft Link Concept

A soft link points to the file path.

If the original file is deleted, the soft link breaks because the path target no longer exists.

### Hard Link vs Soft Link

| Type | Command | What it points to | If original is deleted |
|---|---|---|---|
| Hard link | `ln source linkname` | Inode/data | Link still works |
| Soft link | `ln -s source linkname` | File path | Link breaks |

---

## 16. Check RAM with `free -h`

Check memory usage:

```bash
free -h
```

Example output:

```text
               total        used        free      shared  buff/cache   available
Mem:           1.9Gi       468Mi       723Mi       1.1Mi       880Mi       1.4Gi
Swap:          1.0Gi          0B       1.0Gi
```

Meaning:

| Column | Meaning |
|---|---|
| total | Total RAM/swap |
| used | Used memory |
| free | Free memory |
| buff/cache | Memory used for cache/buffers |
| available | Memory available for applications |

---

## 17. Check Disk Space with `df -h`

Check disk usage:

```bash
df -h
```

Useful when troubleshooting full disks.

Example with `awk`:

```bash
echo "The total disk used is $(df -h | awk 'NR==3 {print $3}')"
```

---

## 18. AWK Practice

`awk` is used to filter columns and rows from command output.

Print first column:

```bash
free -h | awk '{print $1}'
```

Print second column:

```bash
free -h | awk '{print $2}'
```

Print first and second columns:

```bash
free -h | awk '{print $1, $2}'
```

Print only row 2, column 2:

```bash
free -h | awk 'NR==2 {print $2}'
```

Print only row 2, column 4:

```bash
free -h | awk 'NR==2 {print $4}'
```

Print only row 3, column 4:

```bash
free -h | awk 'NR==3 {print $4}'
```

### AWK Concept

```text
NR = row number
$1 = first column
$2 = second column
$3 = third column
```

Example:

```bash
grep -i INFO logfile.log | awk '{print $1, $2}'
```

This finds lines containing `INFO` and prints the first two columns.

---

## 19. Commands Practiced Summary

| Command | Purpose |
|---|---|
| `ssh-keygen` | Generate SSH key pair |
| `ssh -i` | Connect using private key |
| `su` | Switch user |
| `whoami` | Show current user |
| `useradd` | Create user |
| `adduser` | Interactive user creation |
| `passwd` | Set/change password |
| `userdel` | Delete user |
| `usermod -aG` | Add user to group |
| `gpasswd -a` | Add user to group |
| `gpasswd -d` | Remove user from group |
| `getent group` | Check group members |
| `cat /etc/group` | View groups |
| `cat /etc/passwd` | View users |
| `cat /etc/shadow` | View password hashes as root |
| `chmod` | Change permissions |
| `chown` | Change owner/group |
| `apt-get update` | Update package index |
| `apt-get install` | Install package |
| `apt remove` | Remove package |
| `apt purge` | Remove package and config |
| `systemctl status` | Check service status |
| `systemctl start` | Start service |
| `systemctl stop` | Stop service |
| `service` | Manage services older/simple way |
| `lslocks` | View process/file locks |
| `kill -9` | Force kill process |
| `ln` | Create hard link |
| `ln -s` | Create soft link |
| `free -h` | Check RAM usage |
| `df -h` | Check disk usage |
| `awk` | Filter rows/columns |
| `grep -i` | Case-insensitive search |

---

## 20. Key Lessons Learned

- SSH uses public/private key authentication for secure login.
- The public key goes on the target server, and the private key stays with the client.
- `useradd` needs flags like `-m`, but `adduser` is more automatic.
- Users can be added to admin/sudo groups with `usermod -aG sudo username`.
- Linux permissions use read/write/execute values: `4`, `2`, and `1`.
- `chmod` changes permissions, while `chown` changes ownership.
- `systemctl` is the modern way to manage Linux services.
- Hard links survive even if the original filename is deleted.
- Soft links break if the original path is deleted.
- `free -h`, `df -h`, `awk`, and `grep` are very useful for real troubleshooting.

---

## 21. Real Troubleshooting Mindset

This Day 03 practice was not only about memorizing commands. It was about understanding how Linux behaves in real systems:

- How users are created and managed
- How permissions control access
- How services start and stop
- How SSH authentication works
- How to inspect RAM, disk, and process locks
- How to filter command output like a real DevOps/cloud engineer

