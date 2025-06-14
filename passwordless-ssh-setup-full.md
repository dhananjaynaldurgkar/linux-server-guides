
# 🔐 Password-less SSH Setup Across 6 CentOS Servers

This document explains how to set up password-less SSH login between multiple CentOS 10 servers using SSH key-based authentication. It includes two scripts:

- A **single-host script** that copies its SSH key to all other nodes.
- A **full-mesh script** that can be executed on each server to establish trust among all nodes.

---

## ✅ Script 1: Copy SSH Key from One Server to Others

```bash
servers=(server1 server2 server3 server4 server5 server6)

for s in "${servers[@]}"; do
  if [[ "$s" != "$(hostname)" ]]; then
    ssh-copy-id root@$s
  fi
done
```

### 🔍 Explanation

- `servers=(...)`: List of servers.
- `for s in ...`: Loop through each server.
- `$(hostname)`: Get the current host name.
- `ssh-copy-id`: Copies your SSH key to another server for password-less login.

> ⚠️ Make sure the user running this has a public key in `~/.ssh/id_rsa.pub`.

---

## 🔄 Script 2: Full-Mesh SSH Key Exchange (Run on Each Server)

```bash
#!/bin/bash

# Define the server hostnames or IPs
servers=(server1 server2 server3 server4 server5 server6)

# Ensure SSH key exists
if [ ! -f ~/.ssh/id_rsa.pub ]; then
  echo "🔐 No SSH key found. Generating one..."
  ssh-keygen -t rsa -b 4096 -N "" -f ~/.ssh/id_rsa
fi

# Distribute SSH key to other servers
for s in "${servers[@]}"; do
  if [[ "$s" != "$(hostname)" ]]; then
    echo "📤 Copying SSH key to $s..."
    ssh-copy-id root@$s
  else
    echo "⏭️ Skipping self ($s)"
  fi
done
```

### 🧠 Summary

Run this script as `root` or a user with SSH access on all 6 servers. It ensures:

- SSH key is generated if missing
- Key is distributed to every other server
- Each node can SSH into the others without a password

---

## 📌 Best Practices

- Use IPs or FQDNs instead of placeholders like `server1`, etc.
- Make sure SSH and root login are enabled on all target machines.
- Use `ansible` or `expect` for centralized automation if managing many nodes.

