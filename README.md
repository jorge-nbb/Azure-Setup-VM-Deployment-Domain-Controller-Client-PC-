<p align="center">
  <img src="https://upload.wikimedia.org/wikipedia/commons/f/fa/Microsoft_Azure.svg" alt="Azure Lab Setup" />
</p>

<h1 align="center">Azure Setup & VM Deployment (Domain Controller + Client PC)</h1>

---

## 🎯 Project Purpose

Set up a simplified on-premise-like lab in Azure using two virtual machines:

- 🖥️ **Windows Server 2022** (Domain Controller)  
- 💻 **Windows 10 Pro** (Client PC)

This guide will help you simulate a local network environment entirely within Azure for practice and testing.

---

## 🛠️ Tools & Services Used

- **Microsoft Azure** 
- **Azure Resource Groups**
- **Virtual Network (VNet) & Subnet**
- **Network Security Groups (NSG)**
- **Remote Desktop Protocol (RDP)**

---

## ⚙️ Deployment Steps

### STEP 1: Create a Resource Group

<table>
  <tr>
    <td style="vertical-align: top; width: 60%;">
      
1. Go to [Azure Portal](https://portal.azure.com)  
2. In the left-hand menu, click **Resource groups** > **+ Create**  
3. Fill in:
   - Subscription: Default  
   - Name: `Adminlab`  
   - Region: (e.g., `West US`)  
4. Click **Review + Create** > **Create**

</td>
<td style="vertical-align: top; text-align: right; width: 40%;">
  <img src="https://github.com/jorge-nbb/images/raw/main/p1.jpg" alt="1" width="350"/>
  <hr>
  <img src="https://github.com/jorge-nbb/images/raw/main/p2.png" alt="2" width="350"/>
</td>
  </tr>
</table>

---

### STEP 2: Set Up the Virtual Network (VNet)

<table>
  <tr>
    <td style="vertical-align: top; width: 60%;">
      
1. Search: **Virtual networks** > **+ Create**  
2. Configuration:
   - Resource Group: `Adminlab`  
   - Name: `AdminVNet`
   - Region: same as resource group  
3. Click `Next: IP Addresses`
4. Leave the default IPv4 address space as 10.0.0.0/16
5. Add Subnet:
   - Name: `default`  
   - Starting address: `10.0.0.0`
   - Size: `/24`
   - This will auto-fill the Subnet address range to 10.0.0.0 - 10.0.0.255
6. **Review + Create** > **Create**

</td>
<td style="vertical-align: top; text-align: right; width: 40%;">
  <img src="https://github.com/jorge-nbb/images/raw/main/p3.png" alt="3" width="350"/>
  <hr>
  <img src="https://github.com/jorge-nbb/images/raw/main/p4.png" alt="4" width="350"/>
</td>
  </tr>
</table>

---

### STEP 3: Create Network Security Group (NSG)

<table>
  <tr>
    <td style="vertical-align: top; width: 60%;">
      
1. Search: **Network security groups** > **+ Create**  
2. Fill in:
   - Name: `AdminNSG`  
   - Region: same as above  
3. Click **Review + Create** > **Create**

#### Add Inbound Rules:

1. Go to **Inbound security rules** in the settings of the Network security group
2. Click **+ Add**

**Allow RDP (TCP 3389):**

| Setting           | Value       |
|------------------|-------------|
| Source           | Any         |
| Destination Port | 3389        |
| Protocol         | TCP         |
| Action           | Allow       |
| Priority         | 100         |
| Name             | Allow-RDP   |

**Allow ICMPv4 (Ping):**

| Setting           | Value        |
|------------------|--------------|
| Protocol         | ICMP         |
| Action           | Allow        |
| Priority         | 110          |
| Name             | Allow-ICMPv4 |

</td>
<td style="vertical-align: top; text-align: right; width: 40%;">
  <img src="https://github.com/jorge-nbb/images/raw/main/p5.png" alt="5" width="350"/>
  <hr>
  <img src="https://github.com/jorge-nbb/images/raw/main/p7.png" alt="6" width="350"/>
</td>
  </tr>
</table>

---

### STEP 4: Deploy Domain Controller (Windows Server 2022)

<table>
  <tr>
    <td style="vertical-align: top; width: 60%;">
      
1. Go to **Virtual Machines** > **+ Create**  
2. Configuration:
   - Name: `DC`  
   - Image: `Windows Server 2022 Datacenter – Gen2`  
   - Size: `B1s` or `B2s`  
   - Username: `Adminlab`  
   - Password: Strong password  
   - Inbound Ports: **RDP (3389)**  
3. Disk: **Standard HDD**
4. Networking:
   - VNet: `AdminVNet`  
   - Subnet: `default`  
   - NSG: Select `AdminNSG` or reassign later  
5. Click **Review + Create** > **Create**

</td>
<td style="vertical-align: top; text-align: right; width: 40%;">
  <img src="https://github.com/jorge-nbb/images/blob/main/p12.png" alt="7" width="350"/>
  <hr>
  <img src="https://github.com/jorge-nbb/images/blob/main/p15.png" alt="8" width="350"/>
</td>
  </tr>
</table>

---

### STEP 5: Deploy Client PC (Windows 10 Pro)

<table>
  <tr>
    <td style="vertical-align: top; width: 60%;">
      
Same steps as above with these differences:

- Name: `Client`  
- Image: `Windows 10 Pro, Version 22H2 – Gen2`  
- **Confirm license** checkbox for multi-tenant hosting  
- Assign same VNet/Subnet and NSG

</td>
<td style="vertical-align: top; text-align: right; width: 40%;">
  <img src="https://github.com/jorge-nbb/images/raw/main/p17.png" alt="9" width="350"/>
  <hr>
  <img src="https://github.com/jorge-nbb/images/raw/main/p18.png" alt="10" width="350"/>
</td>
  </tr>
</table>

---

### STEP 6: Connect via RDP

<table>
  <tr>
    <td style="vertical-align: top; width: 60%;">
      
1. Open Azure Portal > on both VMs > **Connect > RDP**  
2. Download `.rdp` file and open  
3. Enter:
   - Username: `Adminlab`  
   - Password: the one you set  
4. If prompted with a security warning, click Yes to proceed
5. Inside one VM, open Command Prompt, type: `ipconfig`
   Copy the IPv4 address from this output — this is the private IP of the VM.
6. Go to the second VM, open Command Prompt, and type: `ping [PRIVATE IP of the first VM]`
   - Example: `ping 10.0.0.5`
   - You should see successful replies (4 packets sent/received).

**Always ping the private IP,** not the public one, since both VMs are on the same internal virtual network.

</td>
<td style="vertical-align: top; text-align: right; width: 40%;">
  <img src="https://github.com/jorge-nbb/images/raw/main/p19.png" alt="11" height="250"/>
  <hr>
  <img src="https://github.com/jorge-nbb/images/raw/main/p21.png" alt="12" height="250"/>
  <hr>
  <img src="https://github.com/jorge-nbb/images/raw/main/p22.png" alt="13" height="800"/>
  <hr>
  <img src="https://github.com/jorge-nbb/images/raw/main/p23.png" alt="14" height="800"/>
</td>
  </tr>
</table>

---

## 📝 Results

- Successfully deployed two virtual machines in Azure  
- Both VMs are part of the same virtual network (OnPremVNet)  
- RDP access and internal network communication via ICMP are working  
- Environment is isolated for safe testing and configuration  

This lab is now ready for:

- Promoting **DC-Server** to a Domain Controller   
- Joining **Client-PC** to the domain
- Creating a domain forest 
- Practicing Active Directory, DNS, and domain-based administration 
