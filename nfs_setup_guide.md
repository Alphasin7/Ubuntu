# 📡 Network File System (NFS) on Ubuntu

![NFS Architecture](https://upload.wikimedia.org/wikipedia/commons/thumb/9/9e/Network_File_System.svg/640px-Network_File_System.svg.png)

> **Network File System (NFS)** is a protocol that allows a server to share directories and files with clients over a network. It enables users on client machines to access files as if they were stored locally, providing a simple and efficient way to centralize storage in Unix-like environments.

---

# 🗂️ Ubuntu NFS Server & Client Setup Guide

## 🧠 Overview

This guide explains how to configure **one Ubuntu server as an NFS server** and **multiple Ubuntu clients** to mount the shared directory.

### 🖥️ Example Network

| Role       | IP Address       |
|------------|------------------|
| NFS Server | 192.168.1.100    |
| Client 1   | 192.168.1.101    |
| Client 2   | 192.168.1.102    |

---

## 🛠️ Step 1: Install Packages

### On the **NFS Server**
```bash
sudo apt update
sudo apt install nfs-kernel-server
```

### On the **NFS Clients**
```bash
sudo apt update
sudo apt install nfs-common
```

---

## 🛠️ Step 2: Create and Configure Shared Directory on Server

```bash
sudo mkdir -p /srv/nfs/shared
sudo chown nobody:nogroup /srv/nfs/shared
sudo chmod 777 /srv/nfs/shared
```

Edit the export file:
```bash
sudo nano /etc/exports
```

Add this line:
```
/srv/nfs/shared 192.168.1.0/24(rw,sync,no_subtree_check)
```

Apply the exports:
```bash
sudo exportfs -a
sudo systemctl restart nfs-kernel-server
```

If using UFW:
```bash
sudo ufw allow from 192.168.1.0/24 to any port nfs
```

---

## 🛠️ Step 3: Mount on Clients

Create the mount point:
```bash
sudo mkdir -p /mnt/shared
```

Mount the share:
```bash
sudo mount 192.168.1.100:/srv/nfs/shared /mnt/shared
```

Test file sharing:
```bash
echo "Hello from client1" > /mnt/shared/test.txt
```

---

## 🛠️ Step 4: Auto-Mount on Boot

Edit `/etc/fstab` on each client:

```
192.168.1.100:/srv/nfs/shared /mnt/shared nfs defaults,_netdev 0 0
```

---

## 🔐 Optional Security

Restrict to specific IPs:
```
/srv/nfs/shared 192.168.1.101(rw,sync,no_subtree_check) 192.168.1.102(rw,sync,no_subtree_check)
```

Enable root squashing:
```
/srv/nfs/shared 192.168.1.0/24(rw,sync,no_subtree_check,root_squash)
```

---

## 📚 References
- Ubuntu Docs: https://help.ubuntu.com/community/SettingUpNFSHowTo
- NFS Man Pages: `man exports`

---

> ✅ This guide is suitable for simple setups. For HA NFS or clustered setups, additional steps are required.