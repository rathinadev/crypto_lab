# **Cowrie Honeypot Setup & Monitoring Guide**

## **1. Introduction**
Cowrie is an SSH and Telnet honeypot designed to log brute-force attacks and shell interactions performed by attackers. This guide walks through setting up Cowrie on a Linux system, testing it, monitoring logs, and removing it to restore normal SSH functionality.

---

## **2. Installation Steps**

### **2.1 Update System & Install Dependencies**
Run the following commands to update your system and install necessary dependencies:
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git python3-venv python3-pip python3-dev libssl-dev libffi-dev jq iptables-persistent
```

### **2.2 Clone and Set Up Cowrie**
```bash
cd /opt
sudo git clone https://github.com/cowrie/cowrie.git
sudo chown -R $USER:$USER cowrie
cd cowrie
```

### **2.3 Setup Python Virtual Environment**
```bash
python3 -m venv cowrie-env
source cowrie-env/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

### **2.4 Configure Cowrie**
```bash
cp etc/cowrie.cfg.dist etc/cowrie.cfg
sed -i 's/#listen_port = 2222/listen_port = 2222/' etc/cowrie.cfg
sed -i 's/#enable_telnet = true/enable_telnet = true/' etc/cowrie.cfg
sed -i 's/loglevel = DEBUG/loglevel = INFO/' etc/cowrie.cfg
```

### **2.5 Fix Permissions**
```bash
mkdir -p /opt/cowrie/var/log/cowrie
mkdir -p /opt/cowrie/var/run
chmod -R 755 /opt/cowrie/var/
```

### **2.6 Start Cowrie**
```bash
cd /opt/cowrie
bin/cowrie start
```
Check if Cowrie is running:
```bash
bin/cowrie status
```

---

## **3. Testing Cowrie**

### **3.1 Verify That Cowrie is Listening on Port 2222**
```bash
ss -tulnp | grep 2222
```
Expected Output:
```
LISTEN  0  50  0.0.0.0:2222  0.0.0.0:*  users:("twistd",pid,fd)
```

### **3.2 Test SSH Honeypot**
Try connecting to Cowrie:
```bash
ssh root@localhost -p 2222
```
If successful, you should see a fake SSH shell.

### **3.3 Redirect Port 22 to Cowrie** *(Optional)*
If you want all SSH traffic to go to the honeypot:
```bash
sudo iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222
sudo netfilter-persistent save
```
Now, connecting to `ssh root@localhost -p 22` should also redirect to Cowrie.

---

## **4. Monitoring Cowrie Logs**
View live logs:
```bash
tail -f /opt/cowrie/var/log/cowrie/cowrie.log
```

To see past logins:
```bash
cat /opt/cowrie/var/log/cowrie/cowrie.json
```

---

## **5. Removing Cowrie and Restoring Normal SSH**

### **5.1 Stop Cowrie**
```bash
cd /opt/cowrie
bin/cowrie stop
```

### **5.2 Remove IPTables Rule (if Applied)**
```bash
sudo iptables -t nat -D PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 2222
sudo netfilter-persistent save
```

### **5.3 Restore OpenSSH (if Disabled)**
Check if SSH is running:
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

## **6. Conclusion**
This guide provides a step-by-step approach to setting up, testing, monitoring, and removing Cowrie. By following these steps, you can deploy a honeypot in a lab environment and analyze attacker behavior. 🚀

