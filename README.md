# ☎️ FreePBX – Complete IP PBX System (Asterisk + GUI)

![FreePBX](https://img.shields.io/badge/FreePBX-16.0-green?style=for-the-badge&logo=asterisk)
![Asterisk](https://img.shields.io/badge/Asterisk-18-orange?style=for-the-badge&logo=voipdotms)
![Ubuntu](https://img.shields.io/badge/Ubuntu-Server-blue?style=for-the-badge&logo=ubuntu)
![License](https://img.shields.io/badge/License-MIT-lightgrey?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-In%20Production-success?style=for-the-badge)

---

## 📘 Overview

This repository documents the **entire process of installing, configuring, and optimizing FreePBX**, a full-featured web interface for **Asterisk**, enabling simple and efficient management of extensions, trunks, IVRs, queues, and call reports.

The goal is to provide a **practical and functional guide**, focused on:

- 🚀 **Performance and stability**
- 🔒 **Security (Fail2Ban, TLS, SRTP, strong passwords)**
- 🌐 **Support for local and remote networks (NAT, IPv6, VPN)**
- 🧩 **Integration with professional routers (e.g., Mikrotik)**
- 🏢 **Best practices for enterprise and ISP environments**

---

## ⚙️ Installation Guide

### 1️⃣ Update the System
```bash
sudo apt update && sudo apt upgrade -y
sudo reboot
```

---

### 2️⃣ Install Basic Dependencies
```bash
sudo apt install -y wget curl nano tar gnupg2 cron git net-tools
```

---

### 3️⃣ Install Asterisk
```bash
cd /usr/src
sudo wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-18-current.tar.gz
sudo tar zxvf asterisk-18-current.tar.gz
cd asterisk-18*/

# Install build dependencies
sudo contrib/scripts/install_prereq install

# Configure and compile
./configure
make menuselect
make -j$(nproc)
sudo make install
sudo make samples
sudo make config
sudo ldconfig
```

---

### 4️⃣ Install FreePBX
```bash
cd /usr/src
sudo git clone https://github.com/FreePBX/framework.git freepbx
cd freepbx
sudo ./start_asterisk start
sudo ./install -n
```

After installation, access the web panel at:

```
http://YOUR_IP/admin
```

On first launch, FreePBX will prompt you to create:
- Admin username  
- Password  
- Recovery email  

---

## 🌐 Network Configuration

Go to **Settings → Asterisk SIP Settings**

Configure:
- **External Address:** your public IP  
- **Local Networks:** your internal subnets  
- **NAT:** enabled  
- **RTP Range:** 10000–20000  

Save and apply changes.

---

## 📡 SIP/PJSIP Protocol Configuration

Default channel: **PJSIP**

| Protocol | Port | Type | Status |
|-----------|------|------|--------|
| UDP | 5060 | SIP | ✅ Active |
| TLS | 5061 | SIP | 🔐 Enabled |
| SRTP | — | Media | 🔒 Enabled |

**Other settings:**
- Rewrite Contact → ✅ Enabled  
- RTP Symmetric → ✅ Enabled  
- Force rport → ✅ Enabled  

---

## 🔒 Security & Protection

### 🧱 Fail2Ban
```bash
sudo apt install fail2ban -y
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

### 🔥 Firewall

Block everything and allow only essential ports:

| Port | Protocol | Service |
|------|-----------|----------|
| 22/tcp | SSH | Administration |
| 80/tcp, 443/tcp | HTTP/HTTPS | Web Interface |
| 5060/udp, 5061/udp | SIP/PJSIP | Voice |
| 10000–20000/udp | RTP | Media |

### 🔑 Strong Passwords
Use passwords with **12+ characters**, mixing uppercase, lowercase, numbers, and symbols.

### 🔐 TLS & SRTP
Enable secure calls compatible with modern softphones such as **Zoiper** and **Linphone**.

---

## 🎚️ Quality of Service (QoS)

Prioritize voice traffic on Mikrotik routers:

```bash
/ip firewall mangle
add chain=prerouting protocol=udp port=5060,5061,10000-20000 action=mark-packet new-packet-mark=voip passthrough=yes

/queue tree
add name="VOIP" parent=global packet-mark=voip priority=1
```

This ensures maximum priority for VoIP traffic, reducing latency and jitter.

---

## ⚙️ General Optimizations

- Disable unused FreePBX modules.  
- Use essential codecs only: **alaw**, **ulaw**, **g729**, **opus**.  
- Configure **SRV Records** in DNS for redundancy.  
- Enable **IPv6** when available.  
- Set up **automatic backups**: *Admin → Backup & Restore*.  

---

## 📊 Monitoring and Logs

Useful commands:
```bash
asterisk -rvvvvv
pjsip show endpoints
pjsip show registrations
tail -f /var/log/asterisk/full
```

Web GUI reports:
- **Reports → Asterisk Info**
- **Reports → CDR Reports**

---

## 🔄 Updates & Maintenance

### Update FreePBX Modules
```bash
fwconsole ma upgradeall
fwconsole reload
fwconsole restart
```

### Update the System
```bash
sudo apt update && sudo apt upgrade -y
```

---

## 📈 Additional Recommendations

- 🧠 Set up a **local recursive DNS** (e.g., PowerDNS) for faster lookups.  
- 🧩 Use **RPKI (Routinator or Krill)** to validate BGP prefixes.  
- 📡 ISPs should **separate voice and data VLANs**.  
- 🔐 Use **WireGuard or OpenVPN** for remote extensions.  

---

## 👨‍💻 Author & Credits

**Author:** Kauan Pollicarpo Pereira  
**Project:** Complete VoIP Infrastructure – FreePBX + Asterisk  
**License:** MIT  
**Base System:** Ubuntu Server / Debian  
**Date:** November 2025  

---

## 💡 Acknowledgements

This project is based on practical experience and official documentation from:

- [FreePBX Official Docs](https://www.freepbx.org/support/documentation/)
- [Asterisk Project](https://wiki.asterisk.org/)
- [VoIP Community & Mikrotik Wiki](https://wiki.mikrotik.com/wiki/Manual:VOIP)

---

## 🧩 License

```text
MIT License

Copyright (c) 2025 Kauan Pollicarpo

Permission is hereby granted, free of charge, to any person obtaining a copy
of this documentation and associated files, to deal in the documentation
without restriction, including without limitation the rights to use, copy,
modify, merge, publish, distribute, sublicense, and/or sell copies.
```
