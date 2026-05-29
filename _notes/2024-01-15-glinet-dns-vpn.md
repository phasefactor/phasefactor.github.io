---
title:       "GL.iNet Slate AX as Secure Home Router"
description: "Setting up VPN and DNS security for home use."
tags:        [GL.iNet, Security]
redirect_from: 
 - /2024/01/15/glinet-dns-vpn.html
---

The Slate AX is fairly secure as shipped, except for the DNS configuration.  The default DNS configuration is setup for compatibility--which makes sense because you want your new product to _just work_ when it is plugged in for the first time.

After securing the router's DNS against [surveillance by ISPs and other entities](https://www.howtogeek.com/664608/why-you-shouldnt-be-using-your-isps-default-dns-server/#your-isp-can-log-your-browsing-history), the next step would be using a VPN provider to encrypt all traffic coming from the router.  Since the VPN endpoint will be used by many customers simultaneously, it becomes very difficult for a third party to determine which traffic belongs to which customer. 

***NOTE:*** You will need to turn OFF the DNS protections when you are connecting to a hotel wireless network that uses a captive portal to get your room number before allowing internet access.  The redirect to the captive portal for login is actually done by hijacking DNS requests.


- TOC
{:toc}

---

## Setting up DNS
From the left side navigation, click **Network** and click **DNS**. [(user manual)](https://docs.gl-inet.com/router/en/4/interface_guide/dns/)

### Preventing DNS Hijacking and Leakage
Turn on "DNS Rebinding Attack Protection" and "Override DNS Settings for All Clients".  Overriding client settings will prevent clients on the network from leaking DNS queries by trying to use DNS servers other than the secure ones configured on the router.

### Encrypted DNS
- Select "Encrypted DNS" from the "Mode" drop down.
- Select "DNS over HTTPS" from the "Encryption Type" drop down.
  - [Reason why:](https://www.cloudflare.com/learning/dns/dns-over-tls/) DNS over HTTPS uses port 443 and will preserve privacy because it looks like any normal HTTPS/SSL/TLS traffic.  DNS over TLS is probably better in a non-home network because it allows administrators to filter DNS queries, but uses a custom port that differentiates it from normal HTTPS/SSL/TLS traffic.
- Click "+ Select Servers" in "Servers".
  - Search for "cloudflare", select it, and add it to the list.
  - Consider adding an alternate, such as "quad9-doh-ip4-port443-nofilter-pri".
- Click "Apply" to save. 

### The `hosts` File
Security versus tracking, malware, and ads can be improved with a [custom `hosts` file](https://github.com/StevenBlack/hosts).  However, the basic unified hosts file from Steven Black's repository is over 4MB and will cause the "Edit Hosts" dialog on the DNS configuration page to choke.

Instead, from the command line:
- Run `ssh root@192.168.8.1` (or whatever IP address you have set for your router).
  - Login with the password you configured during initial setup of the router's web interface.
- Run `cp /etc/hosts /etc/hosts.bkup` to make a backup of the default `hosts` file.
- Run `curl https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts >> /etc/hosts` to add the Steven Black block list to `hosts`.
  - This can be validated by running `ls -l /etc/hosts*` which should give output similar to:
    ```
    -rw-r--r--    1 root     root       4649561 Jan 15 10:22 /etc/hosts
    -rw-r--r--    1 root     root           110 Jan 15 10:22 /etc/hosts.bkup
    ```
    (Note the size of `/etc/hosts` is now 4.6MB as expected.)
- Reboot the router.

The drawback to this method is that Steven Black's block list is updated regularly, but this method does not automatically update.

To validate that the block list was loaded after the router reboot:
- Log into the router's web interface.
- From the left side navigation, select "System" and click "Log".
- Use the browser's search feature to search for "hosts".
  - There should be a log entry similar to: 
    ```
    daemon.info dnsmasq[2541]: read /etc/hosts - 152465 addresses
    ```

---

## Port Forwarding / UPnP
UPnP is not available by default on OpenWRT [(reference)](https://forum.gl-inet.com/t/is-upnp-disabled-by-default/19545), and is not recommended because it is a massive security problem.

Instead, explicit port forwarding rules should be used.  Instructions are in [the user manual](https://docs.gl-inet.com/router/en/4/interface_guide/firewall/).

---

## Setting up VPN
The Slate AX supports OpenVPN and WireGuard.  Both are secure, but OpenVPN is more widely supported, while WireGuard has slightly better performance.

### Configuration File from VPN Provider
These procedures are for NordVPN, but ExpressVPN or any other major provider will be very similar.  NordVPN has a [Help Center page](https://support.nordvpn.com/Connectivity/Router/1047409122/GL-iNet-setup-with-NordVPN.htm) for setting up the GL.iNet routers, but these are the basic instructions:

- Go to your [Account Dashboard](https://my.nordaccount.com/dashboard/nordvpn/) on the provider's site.
- Scroll down and find the "Manual Setup" section.  Start the manual setup process.
- NordVPN uses a seperate username and password for your router.  Make note of these "Service Credentials".
- Use the [server tool](https://nordvpn.com/servers/tools/) to select a server and download the configuration file.
- Select the country you want as an end-point and use OpenVPN TCP.  Download the configuration file.

Arguably OpenVPN UDP will be marginally faster, but OpenVPN TCP (at least the NordVPN configuration files I looked at) are using port 443.  The will preserve privacy and deniability since it looks like any normal HTTPS/SSL/TLS traffic.

### Configuring the Slate AX VPN Client
In the router's web interface:
- On the left side of the screen, select **VPN**, then click **OpenVPN Client**.
  - From GL.iNet's [user manual](https://docs.gl-inet.com/router/en/4/interface_guide/openvpn_client/).
- Click **Add Manually**.  This will create a new group, so give it a name.
- Upload the configuration file from NordVPN.
- Enter the Service Credential username and password. ***(Not your normal NordVPN username and password.)***
- Click **Apply**.

The VPN can be started from the OpenVPN Client page or from the VPN Dashboard page.  

**Important:** 
- Go to the VPN Dashboard page and click **Global options**.  [(user manual)](https://docs.gl-inet.com/router/en/4/interface_guide/vpn_dashboard/)
- Turn on "Block Non-VPN Traffic" and "Services from GL.iNet Use VPN".
- The VPN should be in "Global Proxy" mode.
- Click the gear wheel icon for the OpenVPN Client on the VPN Dashboard page.
  - Turn OFF "Allow Remote Access LAN".
  - Turn on "IP Masquerading".



