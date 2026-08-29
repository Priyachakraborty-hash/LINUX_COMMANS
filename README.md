# Linux & DevOps Administration Learning Guide

## Table of Contents
1. [Overview](#overview)
2. [Linux File System Structure](#linux-file-system-structure)
3. [File & Directory Operations](#file--directory-operations)
4. [Text Processing & Manipulation](#text-processing--manipulation)
5. [User & Group Management](#user--group-management)
6. [File Permissions & Ownership](#file-permissions--ownership)
7. [Output Redirection](#output-redirection)
8. [Package Management (YUM)](#package-management-yum)
9. [System Services & Management](#system-services--management)
10. [Jenkins Installation](#jenkins-installation)
11. [Commands Reference](#commands-reference)
12. [Learning Progress](#learning-progress)

---

## Overview

This documentation covers practical **Linux system administration** and **DevOps** skills learned through hands-on command execution. Topics include file system navigation, user management, permissions, text processing, service management, and CI/CD tool setup.

**Environment:** CentOS/RHEL-based Linux

**Learning Goals:**
- Master Linux file system and navigation
- Manage users, groups, and permissions
- Process and manipulate text files
- Manage services and processes
- Deploy and configure Jenkins CI/CD
- Understand output redirection and piping

---

## Linux File System Structure

### Directory Hierarchy

```
/
├── /bin              # Essential user commands
├── /sbin             # System administration binaries
├── /boot             # Boot loader files & kernel
├── /dev              # Device files
├── /etc              # System configuration files
├── /home             # User home directories
├── /opt              # Optional software packages
├── /proc             # Process information (virtual)
├── /root             # Root user home directory
├── /tmp              # Temporary files
├── /var              # Variable data (logs, cache, etc.)
└── /usr              # User programs & libraries
```

### Important Files

| Path | Purpose |
|------|---------|
| `/etc/passwd` | User account information |
| `/etc/group` | Group definitions |
| `/etc/sudoers` | Sudo privilege configuration |
| `/etc/selinux/config` | SELinux settings |
| `/etc/hostname` | System hostname |
| `/var/log/messages` | System log messages |
| `/var/log/yum.log` | Package manager log |
| `/boot/grub2` | GRUB bootloader configuration |

### Examining File System

```bash
# Navigate directories
cd /                    # Go to root
cd /bin                 # Go to bin directory
pwd                     # Print working directory
ls                      # List directory contents

# Explore specific directories
ls /bin                 # View binary executables
ls /sbin                # View system binaries
ls /etc                 # View configuration files
ls /proc                # View process information
ls /dev                 # View device files
ls /var/log             # View log files

# Get file type information
file /bin/pwd           # Show file type
file anaconda-ks.cfg    # Identify file type
```

---

## File & Directory Operations

### Creating & Managing Directories

```bash
# Create single directory
mkdir devdir

# Create nested directories
mkdir -p /opt/dev/ops/devops/test

# List with details
ls -l                   # Long format
ls -lh                  # Human-readable sizes
ls -lr                  # Recursive listing
ls -lt                  # Sort by modification time
ls -ltr                 # Reverse time sort (oldest first)
ls -ld /opt/devopsdir   # Show directory info, not contents

# Change directory
cd ..                   # Go to parent
cd /path/to/dir         # Absolute path
cd                      # Go to home
```

### Viewing File Contents

```bash
# Simple viewing
cat anaconda-ks.cfg             # Display entire file
less anaconda-ks.cfg            # Page through file (interactive)
more anaconda-ks.cfg            # Page through file (simple)

# View specific lines
head anaconda-ks.cfg            # First 10 lines (default)
head -2 anaconda-ks.cfg         # First 2 lines
head -20 anaconda-ks.cfg        # First 20 lines

tail anaconda-ks.cfg            # Last 10 lines (default)
tail -2 anaconda-ks.cfg         # Last 2 lines
tail -f /var/log/messages       # Follow log in real-time

# Count lines
wc -l /etc/passwd               # Count total lines
ls | wc -l                      # Count files in directory
```

### Copying & Linking Files

```bash
# Copy file
cp anaconda-ks.cfg devdir/mybootingfile.cfg

# Create symbolic link (shortcut)
ln -s /opt/dev/ops/devops/test/commands.txt cmds

# List link details
ls -l                           # Shows link information

# Remove link
unlink cmds                     # Delete symbolic link
```

---

## Text Processing & Manipulation

### Filtering & Searching with grep

```bash
# Basic grep
grep firewall anaconda-ks.cfg           # Case-sensitive search
grep -i firewall anaconda-ks.cfg        # Case-insensitive (-i)
grep -v firewall anaconda-ks.cfg        # Invert match (exclude)
grep -vi firewall anaconda-ks.cfg       # Case-insensitive AND exclude

# Recursive search
grep -R SELINUX /etc/*                  # Search all files in /etc
grep -iR firewall *                     # Case-insensitive recursive

# With pipes
tail -20 /var/log/messages | grep -i vagrant    # Search in output
ls | grep host                          # Find files matching pattern
```

### Text Extraction with cut

```bash
# Extract fields from delimited file
cut -d: -f1 /etc/passwd                 # Extract usernames
cut -d: -f3 /etc/passwd                 # Extract UIDs

# Syntax breakdown:
# -d:     Use colon (:) as delimiter
# -f1     Field 1
```

### Pattern Processing with awk

```bash
# Extract fields with awk
awk -F':' '{print $1}' /etc/passwd      # Print first field (usernames)

# Syntax breakdown:
# -F':'   Use colon as field separator
# $1      First field
# $3      Third field
```

### Stream Editing with sed

```bash
# Preview changes (don't modify)
sed 's/co19/c22/g' samplefile.txt       # Substitute (show result)

# Modify file in-place
sed -i 's/co19/c22/g' samplefile.txt    # Save changes to file

# Bulk operations
sed -i 's/co19/c22/g' *                 # Replace in all files

# Syntax breakdown:
# s/       Substitute command
# co19     Search string
# c22      Replacement string
# g        Global (all occurrences)
# -i       In-place editing
```

### Line Counting

```bash
wc -l /etc/passwd                       # Count lines
wc -l </etc/passwd                      # Count lines from input
```

---

## User & Group Management

### Viewing User & Group Information

```bash
# User information
cat /etc/passwd                         # All users on system
cat /etc/group                          # All groups on system
grep vagrant /etc/passwd                # Find specific user
id vagrant                              # User ID and groups
id ansible

# Current user
whoami                                  # Current logged-in user
```

### User Management Commands

```bash
# Create users
useradd ansible                         # Add user
useradd jenkins
useradd aws

# Check newly added users
tail -4 /etc/passwd                     # View last 4 users added
tail -4 /etc/group                      # View last 4 groups added

# Set/Change passwords
passwd ansible                          # Set password for user
passwd jenkins

# Switch users
su - ansible                            # Switch to user (load profile)
exit                                    # Exit to previous user

# Get user info
id ansible                              # Show user ID info
```

### Group Management Commands

```bash
# Create group
groupadd devops                         # Add group

# Add user to group
usermod -aG devops ansible              # Add user to group (-a = append, -G = groups)

# View group membership
grep devops /etc/group                  # Show group members
id ansible                              # Show user's groups

# Delete group
groupdel devops                         # Remove group

# Edit group configuration
vim /etc/group                          # Manual group editing
```

### User Deletion

```bash
# Delete user (keep home directory)
userdel aws

# Delete user with home directory
userdel -r jenkins                      # Remove user & home files
userdel -r aws

# Verify deletion
ls /home/                               # Check home directories
```

### Sudo Configuration

```bash
# Edit sudo permissions
visudo                                  # Edit /etc/sudoers safely

# View sudoers file
ls -l /etc/sudoers                      # Check sudoers permissions

# Usage example: Allow user to run commands without password
# In visudo:
# ansible ALL=(ALL) NOPASSWD: ALL
```

---

## File Permissions & Ownership

### Understanding Permissions

```
-rwxrwxr-x
^ ^ ^ ^ ^
| | | | \-- Others (other)
| | | \---- Group
| | \------ Owner (user)
| \-------- File type (- = file, d = directory, l = link)
\---------- This is part of metadata

Permission Codes:
r (read)    = 4
w (write)   = 2
x (execute) = 1
```

### Changing Ownership

```bash
# Change owner and group
chown -R ansible:devops /opt/devopsdir      # Recursive ownership change
chown aw.devops /opt/webdata/               # Alternative syntax (dot)

# Verify changes
ls -ld /opt/devopsdir                       # Show directory permissions
```

### Changing Permissions

```bash
# Using symbolic notation
chmod o-r /opt/devopsdir                    # Remove read for others
chmod g+w /opt/devopsdir                    # Add write for group
chmod +x /opt/devops                        # Add execute for all

# Using numeric notation
chmod -R 770 /opt/webdata/                  # rwx for user & group, none for others
chmod -R 777 /opt/webdata/                  # Full permissions for all
chmod -R 754 /opt/webdata/                  # rwx=user, r-x=group, r--=others

# Verify changes
ls -ld /opt/webdata/                        # Show directory permissions

# Permission Calculation:
# 7 = 4+2+1 (rwx - full permissions)
# 5 = 4+1   (r-x - read & execute)
# 4 = 4     (r-- - read only)
```

### Practical Examples

```bash
# Create development directory
mkdir /opt/devopsdir
groupadd devops
useradd a

# Set ownership and permissions
chown -R a:devops /opt/devopsdir            # Owner: user 'a', Group: 'devops'
chmod g+w /opt/devopsdir                    # Add write permission for group
chmod o-r /opt/devopsdir                    # Remove read permission for others

# Result: User can do anything, Group can read/write, Others can't access
```

---

## Output Redirection

### Redirection Operators

| Operator | Purpose | Example |
|----------|---------|---------|
| `>` | Redirect to file (overwrite) | `uptime > /tmp/sysinfo.txt` |
| `>>` | Append to file | `uptime >> /tmp/sysinfo.txt` |
| `2>` | Redirect errors | `command 2> /tmp/error.log` |
| `&>>` | Append both stdout & stderr | `command &>> /tmp/error.log` |
| `\|` | Pipe to command | `ls \| grep host` |

### Practical Examples

```bash
# Overwrite file with output
date > /tmp/sysinfo.txt                     # Write date to file (overwrite)
echo "Good morning" > /tmp/greeting.txt     # Write text

# Append to existing file
uptime >> /tmp/sysinfo.txt                  # Append uptime
echo "##############" >> /tmp/sysinfo.txt   # Append separator

# Build system info file
echo "###########################" > /tmp/sysinfo.txt
date > /tmp/sysinfo.txt
echo "##############################" >> /tmp/sysinfo.txt
uptime >> /tmp/sysinfo.txt
echo "##############################" >> /tmp/sysinfo.txt
df -h >> /tmp/sysinfo.txt
echo "##############################" >> /tmp/sysinfo.txt
cat /tmp/sysinfo.txt                        # View final file

# Redirect to null (suppress output)
yum install vim -y > /dev/null              # Hide installation output
free -m > /dev/null                         # Suppress command output
cat /dev/null > /tmp/sysinfo.txt            # Clear file

# Capture errors
freeeeee -m 2 >> /tmp/error.log             # Redirect error output
freesdsd -m -m &>> /tmp/error.log           # Capture all output & errors

# Pipe examples
ls | wc -l                                  # Count files
ls | grep host                              # Filter files
tail -20 /var/log/messages | grep -i vagrant  # Search recent logs
```

---

## Package Management (YUM)

### YUM Commands

```bash
# Search for packages
sudo yum search java-openjdk                # Find Java packages

# Install packages
sudo yum install vim -y                     # Install with auto-yes
sudo yum install httpd -y                   # Install Apache
sudo yum install java-1.8.0-openjdk java-1.8.0-openjdk-devel -y

# List installed packages
yum list installed                          # Show all installed packages

# Update system
sudo yum update -y                          # Update all packages

# Clean package cache
sudo yum clean all                          # Clean yum cache
```

### Repository Management

```bash
# Add repository for Jenkins
sudo tee /etc/yum.repos.d/jenkins.repo > /dev/null << EOF
[jenkins]
name=Jenkins
baseurl=https://pkg.jenkins.io/redhat-stable
gpgcheck=1
gpgkey=https://pkg.jenkins.io/redhat-stable/jenkins.io.key
EOF

# Import GPG key
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
```

---

## System Services & Management

### Service Management with systemctl

```bash
# Check service status
systemctl status httpd                      # Full service status
systemctl is-active httpd                   # Check if running (exit code)
systemctl is-enabled httpd                  # Check if auto-starts

# Start/Stop services
systemctl start httpd                       # Start service
systemctl stop httpd                        # Stop service
systemctl restart httpd                     # Restart service
systemctl reload httpd                      # Reload configuration

# Enable/Disable auto-start
systemctl enable httpd                      # Enable on boot
systemctl disable httpd                     # Disable on boot
```

### System Information

```bash
# Uptime and load
uptime                                      # How long system running
top                                         # Real-time process monitor

# Memory usage
free -m                                     # Free/used memory in MB

# Disk usage
df -h                                       # Disk usage (human-readable)

# Process information
lsof -u aws                                 # List open files by user
last                                        # Login history
who                                         # Currently logged-in users
```

### System Restart

```bash
sudo reboot                                 # Restart the system
```

---

## Jenkins Installation

### Prerequisites Check

```bash
# Verify Java is installed
java -version                               # Check Java version
```

### Step-by-Step Installation

#### 1. Install Java Development Kit

```bash
sudo yum search java-openjdk                # Find Java packages
sudo yum install java-1.8.0-openjdk java-1.8.0-openjdk-devel -y
java -version                               # Verify installation
```

#### 2. Add Jenkins Repository

```bash
# Option 1: Using tee (most reliable)
sudo tee /etc/yum.repos.d/jenkins.repo > /dev/null << EOF
[jenkins]
name=Jenkins
baseurl=https://pkg.jenkins.io/redhat-stable
gpgcheck=1
gpgkey=https://pkg.jenkins.io/redhat-stable/jenkins.io.key
EOF

# Option 2: Using curl (alternative)
sudo curl -fsSL https://pkg.jenkins.io/redhat-stable/jenkins.repo -o /etc/yum.repos.d/jenkins.repo
```

#### 3. Import GPG Key

```bash
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
```

#### 4. Update Package Cache and Install Jenkins

```bash
sudo yum clean all                          # Clean cache
sudo yum update -y                          # Update packages
sudo yum install jenkins -y                 # Install Jenkins
```

#### 5. Verify Jenkins Installation

```bash
java -version                               # Verify Java
rpm -qa | grep jenkins                      # Verify Jenkins installed
```

### Jenkins Service Management

```bash
# Start Jenkins
sudo systemctl start jenkins

# Check status
sudo systemctl status jenkins

# Enable on boot
sudo systemctl enable jenkins

# Verify running
sudo systemctl is-active jenkins
```

### Post-Installation

```bash
# Default Jenkins runs on port 8080
# Access: http://your-server:8080

# Initial setup password location:
cat /var/lib/jenkins/secrets/initialAdminPassword

# Jenkins home directory:
/var/lib/jenkins
```

---

## Commands Reference

### File Navigation & Listing
| Command | Purpose |
|---------|---------|
| `pwd` | Print working directory |
| `cd [path]` | Change directory |
| `ls` | List files |
| `ls -l` | Long format listing |
| `ls -lh` | Long format with human-readable sizes |
| `ls -lt` | Sort by modification time |
| `ls -ltr` | Reverse time sort |

### File Viewing & Searching
| Command | Purpose |
|---------|---------|
| `cat [file]` | Display file contents |
| `head [file]` | Show first 10 lines |
| `head -n [file]` | Show first n lines |
| `tail [file]` | Show last 10 lines |
| `tail -f [file]` | Follow file (live updates) |
| `less [file]` | Page through file |
| `grep [pattern] [file]` | Search for pattern |
| `grep -i [pattern] [file]` | Case-insensitive search |
| `grep -R [pattern] [dir]` | Recursive search |

### Text Processing
| Command | Purpose |
|---------|---------|
| `cut -d: -f1 [file]` | Extract fields |
| `awk -F':' '{print $1}' [file]` | Pattern processing |
| `sed 's/old/new/g' [file]` | Stream editing |
| `sed -i 's/old/new/g' [file]` | In-place edit |
| `wc -l [file]` | Count lines |

### File Operations
| Command | Purpose |
|---------|---------|
| `mkdir [dir]` | Create directory |
| `mkdir -p [path]` | Create nested directories |
| `cp [src] [dest]` | Copy file |
| `ln -s [target] [link]` | Create symbolic link |
| `unlink [link]` | Remove symbolic link |
| `file [file]` | Show file type |

### User & Group Management
| Command | Purpose |
|---------|---------|
| `useradd [user]` | Create user |
| `userdel [user]` | Delete user |
| `userdel -r [user]` | Delete user + home |
| `passwd [user]` | Set password |
| `groupadd [group]` | Create group |
| `groupdel [group]` | Delete group |
| `usermod -aG [group] [user]` | Add user to group |
| `id [user]` | Show user ID info |
| `su - [user]` | Switch user |
| `whoami` | Show current user |

### Permissions & Ownership
| Command | Purpose |
|---------|---------|
| `chown [owner:group] [file]` | Change ownership |
| `chown -R [owner:group] [dir]` | Recursive ownership |
| `chmod [mode] [file]` | Change permissions |
| `chmod -R [mode] [dir]` | Recursive permissions |
| `ls -ld [dir]` | Show directory permissions |

### System & Service Management
| Command | Purpose |
|---------|---------|
| `systemctl start [service]` | Start service |
| `systemctl stop [service]` | Stop service |
| `systemctl status [service]` | Check status |
| `systemctl enable [service]` | Enable on boot |
| `systemctl is-active [service]` | Check if running |
| `uptime` | System uptime |
| `free -m` | Memory usage |
| `df -h` | Disk usage |
| `top` | Process monitor |
| `last` | Login history |
| `who` | Logged-in users |

### Package Management
| Command | Purpose |
|---------|---------|
| `yum search [package]` | Search packages |
| `yum install [package] -y` | Install package |
| `yum update -y` | Update system |
| `yum clean all` | Clean cache |
| `rpm --import [url]` | Import GPG key |
| `rpm -qa` | List all packages |

### Input/Output Redirection
| Operator | Purpose | Example |
|----------|---------|---------|
| `>` | Redirect (overwrite) | `echo "text" > file.txt` |
| `>>` | Append | `echo "text" >> file.txt` |
| `2>` | Redirect errors | `command 2> error.log` |
| `&>>` | Append all output | `command &>> file.log` |
| `\|` | Pipe to command | `cat file \| grep text` |

---

## Troubleshooting Tips

### Common Issues

**Issue: Permission Denied**
```bash
# Check file permissions
ls -l /etc/passwd

# Add execute permission
chmod +x /path/to/file

# Change ownership
chown user:group /path/to/file
```

**Issue: Command not found**
```bash
# Ensure binary location
which [command]

# Check $PATH
echo $PATH
```

**Issue: User already exists**
```bash
# Verify user exists
grep [username] /etc/passwd

# Use different username
useradd [newusername]
```

**Issue: Jenkins won't start**
```bash
# Check logs
tail -f /var/log/jenkins/jenkins.log

# Verify Java installed
java -version

# Check service status
systemctl status jenkins
```

---

## Learning Progress

### Skills Mastered ✓

- [x] Linux file system navigation
- [x] File viewing and editing (cat, less, vim)
- [x] Directory and file operations (mkdir, cp, ln)
- [x] Text processing (grep, sed, awk, cut)
- [x] Output redirection and piping
- [x] User and group management
- [x] File permissions (chmod, chown)
- [x] System information commands
- [x] Package management (yum)
- [x] Service management (systemctl)
- [x] Jenkins CI/CD installation
- [x] Log file monitoring

### Next Steps to Explore

- [ ] **Shell Scripting:** Bash scripts for automation
- [ ] **Cron Jobs:** Schedule automated tasks
- [ ] **Network Configuration:** IP, DNS, firewall
- [ ] **Disk Management:** LVM, partitions, mounting
- [ ] **Process Management:** Background jobs, signals
- [ ] **SSH Keys:** Passwordless authentication
- [ ] **Jenkins Pipelines:** Create CI/CD workflows
- [ ] **Configuration Management:** Ansible, Puppet
- [ ] **Docker Containers:** Containerization basics
- [ ] **Monitoring & Logging:** ELK stack, Prometheus

---

## Quick Reference Cheat Sheet

```bash
# User Management
useradd username
userdel -r username
groupadd groupname
usermod -aG groupname username
passwd username

# Permissions
chmod 755 /path/to/file          # rwxr-xr-x
chown user:group /path/to/file

# Text Searching
grep -i "pattern" file
grep -R "pattern" /directory
sed -i 's/old/new/g' file

# Output Redirection
command > file                    # Overwrite
command >> file                   # Append
command 2>> error.log             # Redirect errors
command | grep "pattern"          # Pipe

# Service Management
systemctl start service
systemctl stop service
systemctl enable service
systemctl status service

# System Info
uptime
free -m
df -h
top
last
```

---


**Status:** Active Learning Document  
**Difficulty Level:** Intermediate  
**Focus Areas:** System Administration, DevOps Fundamentals
