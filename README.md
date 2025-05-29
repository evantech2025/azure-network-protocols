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
- ![Step 4](images/step4_connect_rdp.png)

### Step 5: Install Wireshark
- Download and install Wireshark on the Windows VM.
- Open it and begin capturing traffic.
- ![Step 5](images/step5_install_wireshark.png)

### Step 6: Filter for ICMP and Ping Linux VM
- Filter for `icmp` in Wireshark.
- Ping the Ubuntu VM using its **private IP** from the Windows command line.
- ![Step 6](images/step6_icmp_ping.png)

### Step 7: Ping a Public Website
- In the Windows VM, run:  
  ```powershell
  ping www.google.com
  ```
- Observe ICMP traffic in Wireshark.
- ![Step 7](images/step7_public_ping.png)

---

## Part 3: Configure and Observe Firewall and Protocols

### Step 8: Disable ICMP via Network Security Group (NSG)
- Start a **continuous ping** from Windows to Ubuntu:
  ```powershell
  ping <ubuntu-private-ip> -t
  ```
- Go to the NSG of the Ubuntu VM → Inbound Rules → Deny ICMP.
- Observe ping failure in command prompt and Wireshark.
- ![Step 8](images/step8_nsg_block_icmp.png)

### Step 9: Re-enable ICMP in NSG
- Restore the Inbound ICMP rule.
- Observe ping success resume.
- ![Step 9](images/step9_nsg_allow_icmp.png)

### Step 10: Observe SSH Traffic
- In Wireshark, filter: `ssh`
- From Windows VM PowerShell, connect:
  ```powershell
  ssh labuser@<ubuntu-private-ip>
  ```
- Type a few commands and observe traffic.
- Exit SSH with:
  ```bash
  exit
  ```
- ![Step 10](images/step10_ssh_traffic.png)

### Step 11: Observe DHCP Traffic
- In Wireshark, filter: `bootp` (DHCP protocol).
- In Windows PowerShell (as admin), run:
  ```powershell
  ipconfig /renew
  ```
- Observe DHCP discovery and offer messages.
- ![Step 11](images/step11_dhcp.png)

### Step 12: Observe DNS Traffic
- In Wireshark, filter: `dns`
- Run the following:
  ```powershell
  nslookup google.com
  nslookup disney.com
  ```
- Observe DNS requests and responses.
- ![Step 12](images/step12_dns.png)

### Step 13: Observe RDP Traffic
- In Wireshark, filter:  
  ```plaintext
  tcp.port == 3389
  ```
- Observe continuous RDP traffic even when idle.
- Explanation: RDP streams desktop visuals constantly between client and server.
- ![Step 13](images/step13_rdp_traffic.png)
