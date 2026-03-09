# Report: Configuring a VPN on Windows Server 2025 
====================================================================================0
## 1. Introduction
This report describes the process of installing and configuring a VPN on Windows Server 2025 using the built‑in Remote Access role. The goal is to enable secure remote connectivity for users while ensuring proper routing, authentication, and IP address management.

## 2. Installing the Remote Access Role

### 2.1 Launching Server Manager
After the server boots and loads fully, open Server Manager.
From the top‑right corner, select Manage → Add Roles and Features.
This opens the installation wizard.

### 2.2 Selecting the Server
Choose the primary server from the Server Pool, then continue to the Server Roles section.

### 2.3 Enabling Remote Access
Enable the Remote Access role.
On the Role Services page, select Routing.
When prompted, confirm that Include Management Tools is enabled.
This ensures that all required administrative utilities are installed together with the role.
Once Routing is selected, DirectAccess and VPN (RAS) is automatically included because it is part of the Remote Access feature set.

### 2.4 Installation and Restart
On the Confirmation page, allow the server to restart automatically.
A restart is required because Remote Access modifies core networking components, and Windows must reload these services to apply the configuration safely.
Click Install and wait for the feature to finish installing.

## 3. Installing Remote Access via PowerShell
Open PowerShell from the Administrator profile directory (C:\Users\Administrator).
Running PowerShell from this context ensures full administrative privileges and correct environment variables.

Run the following cmdlets:
`Install-WindowsFeature RemoteAccess -IncludeManagementTools`


This installs the Remote Access role along with all necessary management tools.
`Install-WindowsFeature DirectAccess-VPN -IncludeManagementTools`


This installs the DirectAccess and VPN components required for remote connectivity.
Together, these commands prepare the server for VPN configuration by enabling all required services and administrative consoles.

## 4. Configuring Routing and Remote Access
Return to Server Manager, open the Tools menu, and select Routing and Remote Access.
Right‑click the server name and choose Configure and Enable Routing and Remote Access.
This step activates the service and allows you to define how the server will handle VPN connections.

### 4.1 Choosing the VPN Configuration
Select the first option: Remote Access (Dial‑up or VPN).
Enable VPN under Remote Access.

### 4.2 Selecting the Network Interface
Choose your primary network interface — the one connected to your internal network.

### 4.3 IP Address Assignment
Select Automatically for IP address assignment.
(Important: DHCP must already be configured on the server. The VPN service will request IP addresses from DHCP unless a static pool is defined later.)

### 4.4 RADIUS Server
Choose No, use Routing and Remote Access to authenticate connection requests.
A RADIUS server is a centralized authentication system used in larger organizations.
Since we are configuring a standalone VPN server, RADIUS is not required.
Click Finish to complete the initial setup.

## 5. Configuring IPv4 Address Pool
Return to Routing and Remote Access, right‑click the server name, and open Properties.
Go to the IPv4 tab and select Static address pool.
This ensures that VPN clients receive IP addresses from a controlled, predictable range rather than relying on DHCP.
Add the following pool:

- Start IP: 192.168.1.50
- End IP: 192.168.1.100
  
This range provides dedicated addresses for VPN clients without interfering with the main LAN addressing scheme.
The primary network adapter remains the one connected to your internal network.
Apply the settings and click OK.

## 6. Firewall and Port Forwarding
Configure your firewall and router according to your organization’s security policy.
Typically, VPN servers require forwarding of ports such as:
- UDP 500 (IKE Internet Key Exchange)
- UDP 4500 (NAT‑Traversal)
- ESP protocol (IPsec)
- UDP 1701 (L2TP Layer 2 Tunneling Protocol control channel)   
- ESP (IP protocol 50) – encrypted data channel
- TCP 1723 – PPTP (Point-to-point Tunneling) control channel - if older VPN protocol in use, cryptographically weak though.

Exact requirements depend on the chosen VPN protocol and company standards.

## 7. Granting VPN Access to Users
To allow a user to connect via VPN:
- Open Computer Management.
- Navigate to System Tools → Local Users and Groups → Users.
- Right‑click the desired user account and select Properties.
- Open the Dial‑In tab.
- Under Network Access Permission, choose Allow Access.
- Click Apply.

This grants the user permission to authenticate through the VPN service.

## 8. Conclusion
The steps above outline the full process of installing, configuring, and enabling a VPN on Windows Server 2025. Proper configuration of Remote Access, routing, IP address assignment, and user permissions ensures secure and reliable remote connectivity for your organization.
