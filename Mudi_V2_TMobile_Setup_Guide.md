
# **Mudi V2 Portable 4G LTE Router (GL-E750V2) Setup for T-Mobile and International Travel**

This guide provides a step-by-step process to configure the **Mudi V2 Portable 4G LTE Router (GL-E750V2)** for a **T-Mobile line**, prepare it for **international travel**, and configure the **Fireguard VPN** to ensure that all connected devices appear to be in the **USA**, regardless of your actual location.

Additionally, we will configure a **VPN kill switch** to block internet traffic if the VPN drops, enhancing privacy and security.

---

## **Video Guides: Setting up WireGuard Server**

Before setting up the client, you'll need a WireGuard server. These guides will help you get started:

1. [GL.iNet WireGuard Server Setup Video 1](https://www.youtube.com/watch?v=qLEj9zoiYRs&t=104s&pp=ygUYZ2wuaW5ldCB3aXJlZ3VhcmQgc2VydmVy)
2. [GL.iNet WireGuard Server Setup Video 2](https://www.youtube.com/watch?v=LXbDg1v65Qs&t=409s&pp=ygUYZ2wuaW5ldCB3aXJlZ3VhcmQgc2VydmVy)

Once the server is configured, follow the steps below to configure your **Mudi V2** as a client.

---

## **Table of Contents**

1. [Configure T-Mobile APN Settings](#configure-t-mobile-apn-settings)
2. [Repair/Change IMEI (If Required)](#repairchange-imei-if-required)
3. [Set Up Fireguard VPN for USA Location](#set-up-fireguard-vpn-for-usa-location)
4. [Set Up TTL for Tethering Detection Prevention](#set-up-ttl-for-tethering-detection-prevention)
5. [Testing the Configuration](#testing-the-configuration)
6. [Ensure Router Works Internationally](#ensure-router-works-internationally)
7. [Set Up VPN Kill Switch](#set-up-vpn-kill-switch)
8. [Additional Enhancements](#additional-enhancements)
   - [DNS-over-HTTPS / DNS-over-TLS](#dns-over-https--dns-over-tls)
   - [Ad-blocking](#ad-blocking)
9. [Backup and Restore Configuration](#backup-and-restore-configuration)
10. [Check for IP and DNS Leaks](#check-for-ip-and-dns-leaks)

---

## 1. **Configure T-Mobile APN Settings**

To set up your T-Mobile line on the Mudi V2 router, follow these steps:

1. Access your router’s admin panel by navigating to `192.168.8.1` in a web browser.
2. Log in with the default credentials.
3. Navigate to **Internet** > **Cellular Settings**.
4. Set the **SIM** to **Active**.
5. Configure the APN settings for T-Mobile:
   - **Protocol**: QCM
   - **APN**: `fast.t-mobile.com`
   - **Authentication**: NONE (if required, use PAP or CHAP)
6. Click **Apply** to save the settings.

---

## 2. **Repair/Change IMEI (If Required)**

To configure or reset the IMEI on the Mudi V2 router, use the following AT command:

1. In the admin panel, go to **Internet** > **Modem Management**.
2. Scroll down to the **AT Command** section.
3. Enter the following command:

   ```bash
   AT+EGMR=1,7,"352207821231797"
   ```

Click Send to execute the command.
Note: Ensure the quotes are straight, not curly.

## 3. **Set Up Fireguard VPN for USA Location**

To ensure your devices appear to be located in the USA, configure Fireguard (WireGuard) VPN:

If You Already Have a Fireguard Configuration:
Go to VPN > WireGuard Client in the admin panel.
Click Add a new profile and upload your .conf file or manually configure the USA-based WireGuard VPN.
Enable the VPN and ensure that the traffic is routed through the USA server by enabling the "Force VPN" option.
If You Don’t Have a USA VPN:
Subscribe to a VPN provider with USA servers (e.g., Mullvad, NordVPN, AzireVPN).
Download the WireGuard configuration file for a USA server.
Upload it in the WireGuard Client section.

## 4. **Set Up TTL for Tethering Detection Prevention**

To prevent tethering detection (e.g., by T-Mobile), modify the TTL (Time to Live) settings:

Navigate to Network > Advanced Settings.
Set the TTL value to 64 for both IPv4 and **IPv6`.
This will help disguise tethering as normal mobile device usage.

## 5. **Testing the Configuration**

Restart the Router once the APN, IMEI, and VPN configurations are complete.
Ensure the following:
Your router connects to the T-Mobile network.
The Fireguard VPN routes all traffic through the USA server.
Use iplocation.net to verify that all devices connected to the router appear to be in the USA.

## 6. **Ensure Router Works Internationally**

When traveling, insert a local SIM card, but keep the VPN active. This ensures your traffic still appears to originate from the USA, regardless of your location.

## 7. **Set Up VPN Kill Switch**

To prevent internet access if the VPN connection drops, configure a VPN kill switch using firewall rules:

Access the router via SSH:
```bash
ssh root@192.168.8.1
```

Modify the firewall configuration:
```bash
vi /etc/config/firewall
```

Add the following firewall rules:
```bash
config rule
    option name 'Allow VPN Traffic'
    option src 'lan'
    option dest '*'
    option proto 'all'
    option family 'ipv4'
    option device 'wgclient'
    option target 'ACCEPT'

config rule
    option name 'Block Non-VPN Traffic to WAN'
    option src 'lan'
    option dest 'wan'
    option proto 'all'
    option family 'ipv4'
    option target 'REJECT'
```

Save the file and restart the firewall:
```bash
/etc/init.d/firewall restart
```

Test the Kill Switch:
Disable the VPN and verify that internet traffic is blocked.
Re-enable the VPN and verify that internet access is restored.

## 8. **Additional Enhancements**

**DNS-over-HTTPS / DNS-over-TLS**
You can secure DNS traffic by enabling DNS-over-HTTPS (DoH) or DNS-over-TLS (DoT):

Go to More Settings > Custom DNS Server in the admin panel.
Enable DNS over TLS or DNS over HTTPS.
Use privacy-friendly DNS servers:
Cloudflare: 1.1.1.1
Google: 8.8.8.8
Quad9: 9.9.9.9

**Ad-blocking**
Enable AdGuard Home for network-wide ad-blocking:

Go to Applications > AdGuard Home in the admin panel.
Enable ad-blocking to block ads and trackers across all devices connected to the router.

## 9. **Backup and Restore Configuration**

It’s essential to create a backup of your router’s settings after everything is configured:

Go to System > Backup / Restore in the admin panel.
Click Backup to download your current settings.
Save the backup file for future use.

## 10. **Check for IP and DNS Leaks**

Use the following tools to ensure there are no IP or DNS leaks:

dnsleaktest.com
ipleak.net
These services will help confirm that your traffic is routed through the VPN and that no personal information is being exposed.

**Summary of Commands & Configurations:**
- IMEI Command: AT+EGMR=1,7,"352207821231797"
- T-Mobile APN Settings: fast.t-mobile.com
- Fireguard VPN: Ensure the VPN connects through a USA-based server.
- TTL Settings: Set TTL to 64 for both IPv4 and IPv6.
- VPN Kill Switch: Use firewall rules to block non-VPN traffic.
