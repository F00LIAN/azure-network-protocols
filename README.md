<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines</h1>
In this tutorial, we observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />


<h2>Video Demonstration</h2>

- ### [YouTube: Azure Virtual Machines, Wireshark, and Network Security Groups](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Various Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (21H2)
- Ubuntu Server 20.04

<h2>High-Level Steps</h2>

1. Create and Configure Two Azure VMs (Windows 10 and Ubuntu Linux)

2. Install Wireshark on Windows 10

3. Observe Network Traffic and Protocols
    - ICMP (Ping)
    - OSI Model Layers in Wireshark
    - NSG Rule Creation (Block/Allow ICMP)
    - SSH Traffic (TCP 22)
    - DHCP Traffic (UDP 67/68)
    - DNS Traffic (UDP/TCP 53)
    - RDP Traffic (TCP 3389)

4. Experiment with Network Security Group (firewall rules)

5. Conclusion & Next Steps

<h2>Configuration Steps</h2>

### 1. Create and Configure Two Azure VMs
<p>
<img src="https://github.com/user-attachments/assets/cb68f4f2-fe76-4453-891d-6c3809b782e5" height="60%" width="60%" alt="Azure VM image"/>
</p>
<p>
  
- Create a Resource Group called "RG-Network-Activites".

- Create a virtual network called "Lab2-Vnet" and remember to configure them on both VMs.  
  
- Create a Windows 10 virtual machine in Microsoft Azure called "Windows-VM" in East US. Make sure you have atleast two vCPUs. Make sure it is under the "Lab2-Vnet" virtual network and under the "RG-Network-Activites" resource group. 

- Create a Linux virtual machine with Ubuntu Server called "Linux-VM" in East US. Make sure you have atleast two vCPUs. Make sure it is under the "Lab2-Vnet" virtual network and under the "RG-Network-Activites" resource group.
  
</p>
<br />

### 2. Installing Wireshark
<p>
<img src="https://github.com/user-attachments/assets/722bde3a-3d80-4331-a8ba-25f6c60dcd61" height="60%" width="60%" alt="Disk Sanitization Steps"/>
</p>
<p>

- Using Remote Desktop, connect to the Windows-VM.

- Install a protocol analyzer, [WireShark](https://www.wireshark.org/download.html) to the Windows-VM.

</p>
<br />

<h2>Observing Traffic</h2>

### 3. Observing ICMP Traffic 
<p>
<img src="https://github.com/user-attachments/assets/818970c7-cd86-4f3f-a05d-9eb230a0787c" height="60%" width="60%" alt="ICMP Traffic"/>
</p>
<p>

- Open Wireshark and start packet capture on the ethernet capture. Notice that the computer is communicating quite a bit.

- We need to check for ICMP traffic, therefore we need to ping and test network connectivity to the Linux-VM we created from our existing Windows-VM.

- Open the Windows-VM powershell and enter ping {Linux-VM private IP Address}. 

- The ICMP Echo Request will reach the Linux-VM and the Linux-VM respond with an ICMP Echo Reply.

- Filter wireshark for traffic coming from "icmp" and observe.

- If you wanted nonstop pings, use the "ping {Linux-VM private IP Address} -t"

</p>
<br />

### 4. OSI Model Observations
<p>
<img src="https://github.com/user-attachments/assets/dba4d5e3-be53-497e-bf65-50e4e8f70355" height="60%" width="60%" alt="ICMP Traffic"/>
</p>
<p>

- We can observe the Frame that was sent across the network and notice the Destination and Source Mac Address. See how the Organization Unique Identifier is 12345678.

- We can observe the Internet Protocol (Layer 3 - Internet in OSI) and recognize the destination and source IP address, the type of protocol, and Header Sizes.

- We can then observe the actual payload that was sent across the devices and recognize it is the alphabet. 

</p>
<br />

### 5. Configure a Firewall Network Security Group
<p>
<img src="https://github.com/user-attachments/assets/88cad312-c8e5-4687-a318-303f1898e793" height="60%" width="60%" alt="ICMP Traffic"/>
</p>
<p>

- On the Windows-VM intiate a perpetual ping. In poweshell, enter the "ping {Linux-VM private IP Address} -t". Notice it pings forever.

- Go to the Azure VM Portal → Linux-VM → Network Settings → Network Security Group → Settings → Inbound Security Rules → Create a rule where we block any port, any source, under ICMPv4, and deny access at a high priority.

- Re-enable ICMP traffic for the Network Security Group the Linux-VM has.

- Observe that the ICMP traffic in WireShark should now begin receiving ICMP Echo Reply from the Linux-VM.

- Stop the ping activity.

</p>
<br />

### 6. Observe SSH Traffic (TCP Port 22)
<p>
<img src="https://github.com/user-attachments/assets/b66c9761-ded7-4d15-b46c-d5ba050265f1" height="60%" width="60%" alt="SSH Traffic"/>
</p>
<p>

- Log into the Windows-VM

- Open Wireshark and start packet capture on the ethernet capture. Notice that the computer is communicating quite a bit.

- Filter for ssh traffic under tcp port 22. Notice we do not have any.

- From the Windows-VM open the powershell and ssh into the Linux-VM. "ssh username@<private IP address>". Enter the password configured before. (Remember we use the username we created for the vm for the ssh connection).

- Type commands on the ssh connections: username, pwd, etc.

- Notice the amount of traffic being shared on wireshark for every key stroke. Also notice that the traffic is encrypted when we examine the payload.

- If we were to use TELNET, the packets will be unencrypted.

- Exit the SSH connection by typing 'exit'.

</p>
<br />

### 7. Observe DHCP Traffic (UDP Port 67 and 68)
<p>
<img src="https://github.com/user-attachments/assets/7eb7e15a-63de-4f99-bf77-ca56e70dce64" height="60%" width="60%" alt="DHCP Traffic"/>
</p>
<p>

- Remember DHCP is used to assign an IP address to devices when they are first connected to the network.

- In the Windows-VM, open Wireshark and start packet capture on the ethernet capture. Notice that the computer is communicating quite a bit.

- On Wireshark, filter for DHCP traffic and open powershell to run the command "ipconfig /renew" to request a new ipaddress.

- Witness the Windows-VM requesting(broadcast) a message for a new IP Address. Then the DHCP server replying with an offer.

- Perform advanced filtering and search for  "udp.port == 67 || udp.port == 68"

- Now create a text file on the Windows-10 VM with the following commands "ipconfig /release" and "ipconfig /renew" and save it as dhcp.bat file

- Run the command "dhcp.bat" on powershell and use wireshark to observe DHCP traffic. 

</p>
<br />

### 8. Observe DNS Traffic (UDP Port 53 or TCP Port 53)
<p>
<img src="https://github.com/user-attachments/assets/0e0600eb-b180-454b-8b9d-b969b56609d1" height="60%" width="60%" alt="DNS Traffic"/>
</p>
<p>

- Within the Windows-VM, reset Wireshark and filter for DNS traffic.

- On the powershell, find the IP Address of Disney.com so run "nslookup disney.com".

- Notice the traffic on Wireshark.

</p>
<br />

### 9. Observe RDP (TCP Port 3389)
<p>
<img src="https://github.com/user-attachments/assets/7711942e-0039-44e0-8307-f17719591f38" height="60%" width="60%" alt="RDP Traffic"/>
</p>
<p>

- Within the Windows-VM, reset Wireshark and filter for RDP traffic.

- Notice the constant traffic. Since we are using RDP already. It is streaming a picture from the server to our machine.

- SSH is different because it measures keystroke on the terminal. RDP is streaming picture. 

</p>
<br />

### Conclusion. 

By following the above steps, you have:

  - Deployed two Azure VMs on the same virtual network (Windows 10 and Ubuntu).
  
  - Installed and used Wireshark to capture and analyze multiple protocols (ICMP, SSH, DHCP, DNS, RDP).
  
  - Created and tested Network Security Group rules to allow or deny specific traffic types.
  
  - Explored how the OSI model layers appear in Wireshark captures.

You now have a foundational understanding of how Azure NSGs can secure traffic and how to observe and troubleshoot network behavior using Wireshark.


