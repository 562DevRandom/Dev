
## IP Allocation Schema

| **Section**                  | **IP Range**         | **Subnet Mask**   | **DHCP/Static** | **Notes**                                                                 |
|------------------------------|----------------------|-------------------|-----------------|---------------------------------------------------------------------------|
| DMZ (Unauthenticated Devices)| `192.168.10.0/24`    | `255.255.255.0`   | DHCP            | Up to 254 devices. No QoS guarantees.                                     |
| Secured Admin Section        | `192.168.20.0/24`    | `255.255.255.0`   | Static          | Single IP (`192.168.20.1`) for admin access. No internet traffic allowed. |
| Device Network               | `192.168.30.0/24`    | `255.255.255.0`   | Static          | Up to 25 devices. VLAN-segregated.                                        |
| DMZ (Web Servers)            | `192.168.40.0/24`    | `255.255.255.0`   | Static          | Up to 10 servers initially. IDS/IPS and honeypot enabled.                |
| Monitoring & Logging         | `192.168.50.0/24`    | `255.255.255.0`   | Static          | A single server for sending logs.                                        |


## Configuration Details 

| **Section**                  | **VLAN ID** | **IP Range**         | **DHCP/Static** | **Firewall Rules**                                                                                                                                                                                                 | **Additional Notes**                                                                                   |
|------------------------------|-------------|----------------------|-----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| **DMZ (Unauthenticated)**    | 10          | `192.168.10.0/24`    | DHCP            | - Allow: All outbound traffic to the internet. <br> - Deny: All inbound traffic except established sessions. <br> - Deny: Inter-VLAN communication to VLAN 30-40.                                                                | - DHCP Range: `192.168.10.100-192.168.10.254`. <br> - No QoS guarantees.                              |
| **Secured Admin Section**    | 20          | `192.168.20.0/24`    | Static          | - Allow: SSH (port 22) and RDP (port 3389) from `192.168.20.1` to other sections. <br> - Deny: All inbound/outbound internet traffic. <br> - Logging: Full traffic logging.                                         | - Admin Workstation: `192.168.20.1`. <br>                            |
| **Device Network**           | 30          | `192.168.30.0/24`    | Static          | - Allow: Outbound HTTPS (port 443) to whitelisted update domains. <br> - Deny: All other outbound internet traffic. <br> - Deny: Inter-VLAN communication except for admin-initiated SSH/RDP.                     | - Printers: `192.168.30.1-192.168.30.20`. <br> - Workstations: `192.168.30.21-192.168.30.23`.         |
| **DMZ (Web Servers)**        | 40          | `192.168.40.0/24`    | Static          | - Allow: HTTP (80), HTTPS (443), FTP (21). <br> - Allow: Communication between web servers (`192.168.40.0/24`). <br> - Deny: Traffic to other VLANs unless except Vlan 10.  | - Web Servers: `192.168.40.1-192.168.40.10`. <br> - Honeypot: `192.168.40.xxx`. <br> - Deploy Suricata for IDS/IPS. |
| **Monitoring and Logging**   | 50          | `192.168.50.0/24`    | Static          | - Allow: Custom logging (50), FTP (21). <br> - Allow: incoming communication from web servers/Admin/Device network. <br> - Deny: All Outgoing traffic except to Admin Workstation: `192.168.20.1`.  |  |

## Network Diagram

## Network Diagram (Revised with Two Firewalls)

```plaintext
+-----------------------------+       +-----------------------------+
| Internet Gateway            |       | Unauthenticated DMZ          |
| - Public IP Forwarding      |       | VLAN 10 (192.168.10.0/24)    |
| - Bandwidth: 1 Gbps         |       | - DHCP: 192.168.10.100-254   |
+-----------------------------+       | - No QoS                     |
            |                         +-----------------------------+
            |                                     |
            |                                     |
            v                                     v
+-----------------------------------------------------------------------------+
|                              Edge Firewall/Router                           |
| - Separates DMZ from Internet                                               |
| - VLANs: 10 (Unauthenticated DMZ), 40 (Web Server DMZ)                      |
| - NAT, Basic Filtering, Suricata (IDS/IPS)                                  |
| - Open-Source: pfSense/OPNsense                                             |
+-----------------------------------------------------------------------------+
            |                                     |
            |                                     |
            v                                     v
+-----------------------------+       +-----------------------------+
| Web Server DMZ              |       | Internal Firewall/Router     |
| VLAN 40 (192.168.40.0/24)   |       | - Separates DMZ from Secure  |
| - Servers: 192.168.40.1-10  |       |   Inner Network              |
| - Honeypot: 192.168.40.254  |       | - VLANs: 20,30,50 (Admin,    |
+-----------------------------+       |   Device, Monitoring)        |
                                      | - Strict ACLs, IDS/IPS        |
                                      | - Open-Source: pfSense/OPNsense
                                      +-----------------------------+
                                                      |
                                                      |
                                                      v
                                      +-------------------------------------+
                                      | Secure Inner Network                |
                                      |                                     |
                                      | +-----------------------------+     |
                                      | | Secured Admin Section       |     |
                                      | | VLAN 20 (192.168.20.0/24)   |     |
                                      | | - Static IP: 192.168.20.1   |     |
                                      | +-----------------------------+     |
                                      | +-----------------------------+     |
                                      | | Device Network              |     |
                                      | | VLAN 30 (192.168.30.0/24)   |     |
                                      | | - Printers: 192.168.30.1-20 |     |
                                      | | - Workstations: 21-23       |     |
                                      | +-----------------------------+     |
                                      | +-----------------------------+     |
                                      | | Monitoring & Logging        |     |
                                      | | VLAN 50 (192.168.50.0/24)   |     |
                                      | | - Graylog/ELK Stack          |     |
                                      | | - Zabbix/Nagios              |     |
                                      | +-----------------------------+     |
                                      +-------------------------------------+
```

## Tools
    Firewall: pfSense or OPNsense.
    IDS/IPS: Suricata or Snort.
    Honeypot: Cowrie or T-Pot.
    Monitoring: Zabbix or Nagios.
    Logging: Graylog or ELK Stack.
