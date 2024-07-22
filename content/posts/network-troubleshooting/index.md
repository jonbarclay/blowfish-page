---
title: "Troubleshooting Networking"
date: 2024-04-11
draft: false
author: "Jon Barclay"
tags:
  - netcat
  - DNS
  - Powershell
  - Troubleshooting
description: ""
toc:
---
Troubleshooting network connectivity issues is an essential skill for technicians. In this post, we'll walk through how to identify why something isn't working and resolving the issue. We'll cover testing DNS resolution, checking network connectivity to specific ports, verifying if ports are listening on the server, and examining host-based firewalls.

## Step 1: Testing DNS Resolution with nslookup
The first step in troubleshooting network connectivity is ensuring that DNS correctly resolves the domain name. To do this, we'll use the nslookup command. On Linux and Mac systems, we could also use the host or dig commands, but nslookup works on almost all systems.

Open a command prompt or terminal.
Type ```nslookup <domain_name>```, replacing ```<domain_name>``` with the domain you want to test.
If DNS resolution is working correctly, you should see the IP address associated with the domain name.
Example:
```cmd
C:\> nslookup example.com
Server:  UnKnown
Address:  192.168.1.1

Non-authoritative answer:
Name:    example.com
Address:  93.184.216.34
```
## Step 2: Testing Network Connectivity to a Port
Once you've confirmed that DNS resolution is working, the next step is to test network connectivity to a specific port using either ```netcat``` or the ```Test-NetConnection``` (TNC) command in PowerShell.
#### Using netcat:
Open a command prompt or terminal.
Type ```nc -v <ip_address> <port>```, replacing ```<ip_address>``` with the IP address and <port> with the port number you want to test.
Example:
```bash
$ nc -v 93.184.216.34 80
Connection to 93.184.216.34 80 port [tcp/http] succeeded!
```
#### Using PowerShell TNC command:
Open a PowerShell prompt.
Type ```Test-NetConnection -ComputerName <ip_address> -Port <port>```, replacing ```<ip_address>``` with the IP address and ```<port>``` with the port number you want to test.
Example:
```
PS C:\> Test-NetConnection -ComputerName 93.184.216.34 -Port 80

ComputerName     : 93.184.216.34
RemoteAddress    : 93.184.216.34
RemotePort       : 80
InterfaceAlias   : Ethernet
SourceAddress    : 192.168.1.100
TcpTestSucceeded : True
```
## Step 3: Checking if the Port is Listening on the Server
If the network connectivity test succeeds, the next step is to verify using the ```netstat``` command whether the port is listening on the server.
Open a command prompt or terminal on the server.
Type ```netstat -an | findstr :<port>```, replacing ```<port>``` with the port number you want to check.
Example:
```
C:\> netstat -an | findstr :80
  TCP    0.0.0.0:80             0.0.0.0:0              LISTENING
  TCP    [::]:80                [::]:0                 LISTENING
```
If the port is listening, you should see output indicating that the port is in a LISTENING state.

## Step 4: Checking Host-Based Firewalls
If the port is open and listening but you still can't establish a connection, the issue may be with a host-based firewall such as ```Windows Firewall``` or ```iptables``` on Linux.

#### For Windows Firewall:
Open the Windows Firewall control panel.
Click on "Advanced settings."
Navigate to "Inbound Rules" and check if there are any rules allowing the specific port.

#### For iptables on Linux:
Open a terminal on the server.
Type sudo iptables -L to list the current iptables rules.
```bash
$ sudo iptables -L
```
Check if there are any rules blocking or allowing the specific port.
If you find a firewall rule blocking the port, you'll need to modify the rule or create a new one to allow the desired traffic.

## Conclusion
If at the end of all these steps, you know DNS is working correctly, the server is listening, and there is no host-based firewall blocking the port, you may need to contact the Security team to ask about modifying the firewall rules to allow the traffic or to add the VPN access to your account. By following these steps, you can systematically troubleshoot network connectivity issues and identify the root cause of the problem. Remember to test DNS resolution, check network connectivity to specific ports, verify if ports are listening on the server, and examine host-based firewalls.
