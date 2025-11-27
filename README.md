# Pi-hole-Adblock-Install
This guide provides a comprehensive step-by-step process for deploying Pi-hole on a new, minimal Linux server (e.g., Ubuntu Server, Debian) to function as a network-wide ad and tracker blocker.

🎯 Prerequisites and System Setup1. Host PreparationBegin by logging into your fresh Linux VM (via console or SSH).Update the System:Bashsudo apt update && sudo apt upgrade -y

2. Configure a Static IP AddressA static IP is crucial for a reliable DNS server. We will use the common Netplan utility found on modern Ubuntu/Debian systems.Find your YAML configuration file:Bashls /etc/netplan/
(The file name is typically something like 00-installer-config.yaml.)Edit the file (using nano as the editor):Bashsudo nano /etc/netplan/00-installer-config.yaml
Configure the interface (replace eth0 with your actual interface name like ens18 if necessary, and use your actual network details):YAMLnetwork:
  version: 2
  renderer: networkd
  ethernets:
    ens18:           # <-- Your actual network interface name
      dhcp4: no      # <-- Disable DHCP
      addresses: [172.16.144.81/24] # <-- YOUR PI-HOLE STATIC IP
      routes:
        - to: default
          via: 172.16.144.1        # <-- YOUR GATEWAY/ROUTER IP
      nameservers:
        addresses: [127.0.0.1]     # <-- Use the local loopback for initial DNS
   
Save (Ctrl+O, Enter) and Exit (Ctrl+X).Apply the changes:Bashsudo netplan apply

2. Pi-hole InstallationThe official Pi-hole installer script manages all dependencies and configuration.Execute the Installer:Bashcurl -sSL https://install.pi-hole.net | bash
Installer Prompts: Follow the on-screen prompts:Interface: Select your network interface (ens18).Upstream DNS Provider: Choose a public provider (e.g., Cloudflare $1.1.1.1$) that Pi-hole will forward non-blocked queries to.Blocklists: Accept the default lists.Static IP Confirmation: The installer will confirm the static IP you set in the previous step. Confirm and proceed.Web Admin Interface: Select On.Web Server (Lighttpd): Select On.Note the Password: The final screen will display the randomly generated Web Interface Password. Note it down immediately.Set a Custom Password (Optional):Bashsudo pihole -a -p

4. Verification and Testing3.1 Check Service HealthSince we configured the static IP and used $127.0.0.1$ for DNS in the Netplan file, Pi-hole FTL should start and bind to Port 53 without issue.Check Status:Bashsudo service pihole-FTL status
Expected Result: Active: active (running) with no "Address in use" errors in the recent logs.3.2 Verification TestTo prove Pi-hole is intercepting and blocking, we will test a known ad domain.Blacklist a Test Domain: This ensures the domain is on the list for a clear test result.Bashsudo pihole deny ads.doubleclick.net
Test the DNS Lookup: Run dig to confirm the domain is blocked.Bashdig ads.doubleclick.net
Expected Success: The ;; SERVER: line should show $127.0.0.1$ and the ANSWER SECTION should return the block IP $0.0.0.0$.4. Network Deployment (Go-Live)Pi-hole is now working on the VM. The final step is to make it the default DNS server for all devices on your network.4.1 Access the DashboardVerify the setup is accessible from your computer via the web browser:http://172.16.144.81/admin
4.2 Configure Your RouterLog into your router's administration panel.Find the DHCP Server Settings or LAN/Network Settings.Change the Primary DNS Server address that your router hands out to clients to the Pi-hole VM's IP: 172.16.144.81.Save the settings and reboot your router to ensure all clients receive the new configuration.All devices that connect to your router will now use Pi-hole to filter ads and trackers!
