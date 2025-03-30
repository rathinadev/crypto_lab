# **Cowrie Honeypot Troubleshooting Guide**

This guide provides solutions for common issues encountered while setting up and running Cowrie, explaining the cause and resolution for each problem.

---

## **1. Permission Issues While Copying Configuration File**

### **Problem:**

```
cp: cannot stat 'cowrie.cfg.dist': No such file or directory
```

### **Cause:**

The configuration file path is incorrect or the command is being run from the wrong directory.

### **Fix:**

```bash
cd /opt/cowrie/etc
cp cowrie.cfg.dist cowrie.cfg
```

Ensure you are in the correct directory before running the command.

---

## **2. Address Already in Use (Port Conflict)**

### **Problem:**

```
twisted.internet.error.CannotListenError: Couldn't listen on 0.0.0.0:2222: [Errno 98] Address already in use.
```

### **Cause:**

Cowrie (or another process) is already using port 2222.

### **Fix:**

1. Check which process is using port 2222:
   ```bash
   sudo ss -tulnp | grep 2222
   ```
2. Stop the running Cowrie process:
   ```bash
   cd /opt/cowrie
   bin/cowrie stop
   ```
   Or manually kill the process:
   ```bash
   sudo kill -9 <PID>  # Replace <PID> with the actual process ID from the previous command
   ```
3. Restart Cowrie:
   ```bash
   bin/cowrie start
   ```
4. Verify it's running:
   ```bash
   bin/cowrie status
   ```

---

## **3. SSH Key Mismatch Warning**

### **Problem:**

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
```

### **Cause:**

Cowrie generated a new SSH key, and it differs from the previously stored one in `~/.ssh/known_hosts`.

### **Fix:**

Remove the old SSH key entry:

```bash
ssh-keygen -f "/home/$USER/.ssh/known_hosts" -R "[localhost]:2222"
```

Then, retry SSH connection:

```bash
ssh root@localhost -p 2222
```

---

## **4. SSH Password Always Incorrect**

### **Problem:**

```
root@localhost's password:
Permission denied, please try again.
```

### **Cause:**

Cowrie does not use system passwords. It uses credentials defined in `/opt/cowrie/etc/userdb.txt`.

### **Fix:**

1. Open the user database file:
   ```bash
   nano /opt/cowrie/etc/userdb.txt
   ```
2. Add valid credentials (username and password pair):
   ```
   root:root
   admin:admin
   user:password
   ```
3. Save the file and restart Cowrie:
   ```bash
   bin/cowrie restart
   ```
4. Retry SSH login:
   ```bash
   ssh root@localhost -p 2222
   ```

---

## **5. Cowrie Not Logging Data**

### **Problem:**

No logs are being recorded in `/opt/cowrie/var/log/cowrie/cowrie.log`.

### **Cause:**

Cowrie's log directory might not have the correct permissions.

### **Fix:**

```bash
mkdir -p /opt/cowrie/var/log/cowrie
chmod -R 755 /opt/cowrie/var/
```

Restart Cowrie:

```bash
bin/cowrie restart
```

Check logs again:

```bash
tail -f /opt/cowrie/var/log/cowrie/cowrie.log
```

---

## **6. Restoring OpenSSH After Testing Cowrie**

### **Problem:**

After testing Cowrie, normal SSH access is blocked or redirected.

### **Fix:**

1. Stop Cowrie:
   ```bash
   bin/cowrie stop
   ```
2. Remove IPTables redirect rule (if applied):
   ```bash
   sudo iptables -t nat -D PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222
   sudo netfilter-persistent save
   ```
3. Ensure OpenSSH is running:
   ```bash
   sudo systemctl status ssh
   ```
   If inactive, start it:
   ```bash
   sudo systemctl start ssh
   ```
   If OpenSSH was removed, reinstall it:
   ```bash
   sudo apt install openssh-server -y
   sudo systemctl enable ssh
   sudo systemctl start ssh
   ```

---

## **7. Conclusion**

This document outlines common issues faced while setting up and running Cowrie, their causes, and how to fix them. Following this guide ensures a smooth experience during presentations or lab sessions. 🚀

