# 🔐 Password-less SSH Login Between 6 CentOS 10 Servers

Set up SSH key-based authentication to allow seamless, password-less access between 6 CentOS 10 servers.

---

## ✅ Step 1: Generate SSH Key on Each Server

On **each** of the 6 servers, run the following:

```bash
ssh-keygen -t rsa -b 4096
```

- Press `Enter` to accept the default path (`~/.ssh/id_rsa`)
- Leave the passphrase **empty** (just press `Enter` twice)

This will generate:
- `~/.ssh/id_rsa` (private key)
- `~/.ssh/id_rsa.pub` (public key)
- to check these files you can run command - ll .ssh/
```bash
[dnaldurgkar@linserv01 ~]$ ll .ssh/
total 20
-rw-------. 1 dnaldurgkar dnaldurgkar  747 Jun 14 07:51 authorized_keys
-rw-------. 1 dnaldurgkar dnaldurgkar 3389 Jun 14 07:55 id_rsa
-rw-r--r--. 1 dnaldurgkar dnaldurgkar  747 Jun 14 07:55 id_rsa.pub
-rw-------. 1 dnaldurgkar dnaldurgkar  837 Jun 14 07:52 known_hosts
-rw-r--r--. 1 dnaldurgkar dnaldurgkar   95 Jun 14 07:52 known_hosts.old
[dnaldurgkar@linserv01 ~]$
```


---

## ✅ Step 2: Copy Public Keys to Other Servers

Use `ssh-copy-id` to share the public key with other servers.

Example: From **server1** to **server2**:

```bash
ssh-copy-id root@server2
```

> Enter the password **only once** per server.

Repeat this process for all server pairs.  
There are **30 unique directions** (6 × 5 = 30).

---

## 🔁 Optional: Automate with a Loop

Create a list of hostnames or IPs:

```bash
servers=(server1 server2 server3 server4 server5 server6)

for s in "${servers[@]}"; do
  if [[ "$s" != "$(hostname)" ]]; then
    ssh-copy-id root@$s
  fi
done
```

> Adjust `root` if using a different SSH user.

---

## ✅ Step 3: Set Proper Permissions

Ensure SSH folders and files are properly secured:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

## ✅ Step 4: Test Password-less Login

From one server, attempt to SSH into another:

```bash
ssh server2
```

You should log in **without being prompted for a password**.

---

## 🔒 Optional: Enforce SSH Key Authentication

On each server, edit the SSH config:

```bash
vi /etc/ssh/sshd_config
```

Ensure these lines are set:

```text
PubkeyAuthentication yes
PasswordAuthentication no
```

Then reload the SSH service:

```bash
systemctl reload sshd
```

---

## 🛠️ Tools to Manage Keys (Optional)

- `ansible` for automated distribution
- `ssh-agent` for session-based key caching
- `FreeIPA` or `LDAP` for centralized auth

---

## 📌 Summary

By following these steps, you’ll achieve:

- 🔐 Secure, password-less SSH between servers  
- ⏱️ Faster and automated admin tasks  
- 🔄 Peer-to-peer communication for clustering, orchestration, or automation

---
