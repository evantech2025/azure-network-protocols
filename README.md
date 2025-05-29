<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines using Wireshark</h1>
In this tutorial, we will observe various network traffic to and from Azure Virtual Machines with Wireshark as well as experiment with Network Security Groups. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines)
- Remote Desktop (RDP)
- Command-Line Tools
- Various Network Protocols (ICMP, SSH, DHCP, DNS, RDP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 (22H2)
- Ubuntu Server 22.04

<h2>High-Level Steps</h2>

- Create a Resource Group
- Create a Virtual Machine
- Observe ICMP Traffic
- Observe SSH Traffic
- Observe DHCP Traffic
- Observe DNS Traffic
- Observe RDP Traffic

<h2>Actions and Observations</h2>

---

## Part 1: Create Virtual Machines

### Step 1: Create a Resource Group
- Navigate to [https://portal.azure.com](https://portal.azure.com)
- Create a new Resource Group.
  ![Step 1](https://github.com/user-attachments/assets/483fe16d-4d02-41b6-aab8-be498c9908d2)

### Step 2: Create a Windows 10 Virtual Machine
- Use the previously created Resource Group.
- Let Azure automatically create a Virtual Network (VNet) and Subnet.
  ![Step 2](https://github.com/user-attachments/assets/ac69d82b-a7d4-44e8-831e-5e5844eb2f56)

### Step 3: Create a Linux (Ubuntu) Virtual Machine
- Use the **same** Resource Group and **same VNet** created in Step 2.
- Authentication type: Username & Password.
- Ensure both VMs are in the **same VNet and Subnet**.
  ![Step 3](https://github.com/user-attachments/assets/0dae36f5-e560-4c60-b230-94f21cdaa3e3)
  ![Step 3](https://github.com/user-attachments/assets/2f38cf0a-5835-4e47-bb4c-be2f99fa4fc3)

---

## Part 2: Observe ICMP Traffic

### Step 4: Connect to Windows VM
- Use Microsoft Remote Desktop (install it if you're on Mac).
- Connect to your Windows VM.
 ![Step 4](https://github.com/user-attachments/assets/0e368e38-6dae-4e06-8c26-f7f43b30fcd9)

### Step 5: Install Wireshark
- Download and install [Wireshark](https://www.wireshark.org) on the Windows VM.
- Open it and begin capturing traffic (Ethernet is being captured).
 ![Step 5](https://github.com/user-attachments/assets/3271a23f-cd28-43d0-82fd-b6819dd6e0ea)

### Step 6: Filter for ICMP and Ping Linux VM
- Filter for `icmp` in Wireshark.
- Ping the Ubuntu VM using its **private IP** from the Windows command line.
 ![Step 6](https://github.com/user-attachments/assets/9cc248a5-8851-45a8-84f4-c9b298e5dc9d)
 ![Step 6](https://github.com/user-attachments/assets/1bd74555-1e13-4053-89ae-06a71c42a406)


### Step 7: Ping a Public Website
- In the Windows VM, run:  
  ```powershell
  ping www.google.com
  ```
- Observe ICMP traffic in Wireshark.
 ![Step 7](https://github.com/user-attachments/assets/a38c34a8-9185-4013-9777-93af58205b69)

---

## Part 3: Configure and Observe Firewall and Protocols

### Step 8: Disable ICMP via Network Security Group (NSG)
- Start a **continuous ping** from Windows to Ubuntu:
  ```powershell
  ping <ubuntu-private-ip> -t
  ```
- Go to the NSG of the Ubuntu VM → Inbound Rules → Deny ICMP.
- Observe ping failure in command prompt and Wireshark.
 ![Step 8](https://github.com/user-attachments/assets/1cc1bba5-21c4-4623-bef6-69a660a5b50c)
 ![Step 8](https://github.com/user-attachments/assets/808e3a78-57bd-45c3-8859-d421d02c699e)


### Step 9: Re-enable ICMP in NSG
- Restore the Inbound ICMP rule.
- Observe ping success resume.
 ![Step 9](https://github.com/user-attachments/assets/073388af-0016-459f-be54-944cb7a29d33)
 ![Step 9](https://github.com/user-attachments/assets/2c02432f-2ecc-4c13-a43d-019a80c3b787)


### Step 10: Observe SSH Traffic
- In Wireshark, filter: `ssh`
- From Windows VM PowerShell, connect:
  ```powershell
  ssh labuser@<ubuntu-private-ip>
  ```
- Type a few commands and observe traffic.
- Exit SSH with:
  ```
  exit
  ```
 ![Step 10](https://github.com/user-attachments/assets/5e02c882-cfa0-4613-967e-9b0039ec78ea)

### Step 11: Observe DHCP Traffic
- In Wireshark, filter: `dhcp` (DHCP protocol).
- In Windows PowerShell (as admin), run:
  ```powershell
  ipconfig /renew
  ```
- Observe DHCP request and acknowledge messages.
 ![Step 11](https://github.com/user-attachments/assets/fcd8618b-7282-4244-9382-6f90ec221067)

### Step 12: Observe DNS Traffic
- In Wireshark, filter: `dns`
- Run the following:
  ```powershell
  nslookup google.com
  nslookup disney.com
  ```
- Observe DNS requests and responses.
 ![Step 12](https://github.com/user-attachments/assets/db95b16a-18d8-4a76-aab6-8f78ee7993ae)

### Step 13: Observe RDP Traffic
- In Wireshark, filter:  
  ```
  tcp.port == 3389
  ```
- Observe continuous RDP traffic even when idle.
- Explanation: RDP streams desktop visuals constantly between client and server.
 ![Step 13](https://github.com/user-attachments/assets/eabb91c8-096b-4b0f-a6e0-ae570cbd00ff)

