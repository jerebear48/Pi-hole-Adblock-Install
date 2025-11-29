# Pi-hole Installation Guide (Linux)
Pi-hole is a network-wide ad blocker that works as a DNS sinkhole. This guide covers how to install Pi-hole on a Linux system such as Ubuntu, Debian, or Raspberry Pi OS.

### Prerequisites
- A Linux machine (Ubuntu/Debian/Raspberry Pi OS recommended)
- Root or sudo privileges
- A static IP address (recommended for Pi-hole stability)
- An internet connection

### Step 1: Update Your System

Run the following commands to ensure your packages are up to date:
```
sudo apt update && sudo apt upgrade -y
```

### Step 2: Install Pi-hole Using the Automated Installer

Pi-hole provides a one-command automatic installation script.

Run:
```
curl -sSL https://install.pi-hole.net | bash
```
You will be guided through several configuration prompts.

### Step 3: Pi-hole Configuration

During the installation, you'll be asked to configure:

<ins>**Static IP Address**</ins> 

Pi-hole will warn you if your system does not have a static IP. It is recommended to allow the installer to configure one.

<ins>**Upstream DNS Provider**</ins> 

Choose an upstream DNS server:
- Cloudflare
- Google
- OpenDNS
- Quad9

<ins>**Blocklists**</ins> 

Leave the default blocklists or add custom ones later.

<ins>**Admin Web Interface**</ins> 

Select Yes to install the web interface.

<ins>**Logging Mode**</ins> 

You may choose:

- Show all domains
- Ignore some domains
- No logging

<ins>**Privacy Level**</ins> 

Choose how much data Pi-hole keeps.

### Step 4: Access the Web Dashboard

After installation, Pi-hole provides a URL similar to:
```
http://<your-pi-ip-address>/admin
```
Log in using the randomly generated admin password displayed at the end of the setup.

If you missed it, you can reset it with:
```
pihole -a -p
```

### Step 5: Local DNS Configuration

Pi-hole will begin running immediately after installation. No router changes are required for this setup.

You may choose to manually set your Linux system to use Pi-hole as its DNS resolver:
```
echo "nameserver 127.0.0.1" | sudo tee /etc/resolv.conf
```
This forces the local machine to use Pi-hole directly.

### Step 6: Optional — Enable DHCP Server

Pi-hole can replace your router’s DHCP server. Enable it in:
```
Settings → DHCP
```
Then disable DHCP on your router.

### Step 7: Adding Blocklists

Pi-hole supports custom blocklists to expand ad and tracker blocking. You can add them via the web interface or command line.

Where to Add Blocklists (Web Interface)

1. Go to:
```
http://<your-pi-ip-address>/admin
```
2. Navigate to Group Management → Adlists

3. Click Add and paste a blocklist URL

4. Update gravity:
```
pihole -g
```
### Recommended Blocklists

Here are a few commonly used, safe blocklists:

1. **StevenBlack Unified Hosts**

A well‑maintained, popular blocklist combining multiple reputable sources.
```
https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts
```
2. **OISD Blocklist (Full)**

Very clean and actively maintained — minimal false positives.
```
https://dbl.oisd.nl/
```
3. **Energized Protection (Blu)**

Moderate‑sized list, good balance.
```
https://block.energized.pro/blu/formats/hosts
```
4. **Adaway Mobile Ads List**
```
https://adaway.org/hosts.txt
```
Good for blocking mobile ad networks.

### Useful Commands

| Command  | Description |
| ------------- | ------------- |
| pihole status  | Check Pi-hole status  |
| pihole restartdns  | Restart DNS service  |
| pihole -up  | Update Pi-hole  |
| pihole -a -p  | Change admin password  |
| pihole -t  | Real-time query log  |

### You're Done!

Your Pi-hole server should now be actively blocking ads and unwanted domains across your network.

Feel free to customize blocklists or integrate with Unbound for recursive DNS.
