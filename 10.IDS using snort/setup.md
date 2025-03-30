# Intrusion Detection System (IDS) using Snort

## **Objective**

To demonstrate the working of an Intrusion Detection System (IDS) using Snort in Linux.

---

## **Installation Steps**

### **Step 1: Update System**

```bash
sudo apt update && sudo apt upgrade -y
```

### **Step 2: Install Snort**

```bash
sudo apt install -y snort
```

During installation, set the **HOME\_NET** value when prompted. If unsure, you can modify it later.

### **Step 3: Verify Installation**

```bash
snort -V
```

### **Step 4: Configure Snort**

1. Open the Snort configuration file:
   ```bash
   sudo nano /etc/snort/snort.conf
   ```
2. Locate the line:
   ```snort
   var HOME_NET any
   ```
   Modify it to match your network:
   ```snort
   var HOME_NET 192.168.1.0/24
   ```
3. Save and exit (`CTRL + X`, then `Y`, then `Enter`).

### **Step 5: Create Log Directory**

```bash
sudo mkdir /var/log/snort
sudo chmod 777 /var/log/snort
```

---

## **Running Snort in Different Modes**

### **1. Sniffer Mode (Packet Capturing)**

- Display packet headers:
  ```bash
  sudo snort -v
  ```
- Display packet headers with application data:
  ```bash
  sudo snort -vd
  ```

### **2. Packet Logger Mode**

- Log packets to a directory:
  ```bash
  sudo snort -dev -l /var/log/snort
  ```
- Log packets for a specific network:
  ```bash
  sudo snort -dev -l /var/log/snort -h 192.168.1.0/24
  ```

#### **Viewing Logged Packets**

After running Snort in logging mode, check the logs:

```bash
ls /var/log/snort/
```

To view a log file:

```bash
sudo snort /var/log/snort/snort.log.*
```

---

### **3. Network Intrusion Detection System (NIDS) Mode**

#### **Step 1: Create a Custom Rule**

Edit the **local.rules** file:

```bash
sudo nano /etc/snort/rules/local.rules
```

Add the following rule to detect ICMP ping requests:

```snort
alert icmp any any -> any any (msg:"ICMP Ping Detected"; sid:1000001; rev:1;)
```

Save and exit (`CTRL + X`, then `Y`, then `Enter`).

#### **Step 2: Enable the Rule in snort.conf**

Edit Snort's configuration file:

```bash
sudo nano /etc/snort/snort.conf
```

Ensure the following line is **uncommented** or **added**:

```snort
include $RULE_PATH/local.rules
```

Save and exit.

#### **Step 3: Run Snort in IDS Mode**

```bash
sudo snort -A console -q -i eth0 -c /etc/snort/snort.conf
```

*(Replace **``** with your network interface, check using **``**)*

#### **Step 4: Store IDS Logs**

Run Snort with logging enabled:

```bash
sudo snort -d -h 192.168.1.0/24 -c /etc/snort/snort.conf -l /var/log/snort
```

#### **Step 5: Test IDS with ICMP Ping**

Open another terminal and run:

```bash
ping -c 5 8.8.8.8
```

Check if Snort detects the ping by viewing logs:

```bash
cat /var/log/snort/alert
```

Expected output:

```
[**] [1:1000001:1] ICMP Ping Detected [**]
```

---

## **Conclusion**

This experiment demonstrated how to install and configure Snort in Linux, use it in different modes, set up a detection rule, and verify its functionality by testing with ICMP ping requests. Snort is an effective open-source IDS for real-time network traffic monitoring and intrusion detection.

