# Linux Commands Cheat Sheet

## File System Commands

| Command | Description |
|---|---|
| `pwd` | Show current working directory |
| `ls -l` | List files with detailed permissions |
| `cd` | Change directory |
| `mkdir project` | Create new directory |
| `touch file.txt` | Create empty file |
| `cp file1 file2` | Copy files |
| `mv old.txt new.txt` | Move or rename file |
| `rm -rf folder/` | Remove directory recursively |
| `cat file.txt` | View file contents |
| `echo "Hello"` | Print text to terminal |

---

## Permissions & Ownership

| Command | Description |
|---|---|
| `chmod +x script.sh` | Make script executable |
| `chmod 644 file.txt` | Set read/write permissions |
| `chmod 755 script.sh` | Give execute permissions |
| `chown user:user file.txt` | Change file ownership |

---

## Process Management

| Command | Description |
|---|---|
| `ps aux` | Show running processes |
| `ps aux \| grep bash` | Search running process |
| `top` | Monitor system resources live |
| `kill -9 PID` | Force terminate process |
| `jobs` | Show background jobs |
| `sleep 10` | Pause command for 10 seconds |

---

## Networking Commands

| Command | Description |
|---|---|
| `ping google.com` | Test internet connectivity |
| `ip a` | Show IP addresses/interfaces |
| `curl https://example.com` | Send HTTP request |
| `wget https://example.com` | Download webpage/file |
| `netstat -tulnp` | Show listening ports |
| `ss -tulnp` | Modern port/socket viewer |
| `dig google.com` | DNS lookup |
| `nslookup google.com` | Simple DNS query |
| `traceroute google.com` | Trace network path |
| `hostname -I` | Show server IP address |

---

## Logs & Services

| Command | Description |
|---|---|
| `journalctl -xe` | View system logs |
| `systemctl status ssh` | Check SSH service status |
| `systemctl start nginx` | Start nginx service |
| `systemctl stop nginx` | Stop nginx service |

---

## Compression & Archives

| Command | Description |
|---|---|
| `tar -cvf logs.tar logs/` | Create tar archive |
| `tar -xvf logs.tar` | Extract tar archive |
| `gzip file.txt` | Compress file |
| `gunzip file.txt.gz` | Decompress gzip file |

---

## Automation

| Command | Description |
|---|---|
| `crontab -l` | List scheduled cron jobs |
| `crontab -e` | Edit cron jobs |

### Example Cron Job

```bash
* * * * * echo "Cron worked at $(date)" >> /tmp/cron.log
