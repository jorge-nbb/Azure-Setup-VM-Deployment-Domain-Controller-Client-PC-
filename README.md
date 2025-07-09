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

1. Go to [Azure Portal](https://portal.azure.com)  
2. In the left-hand menu, click **Resource groups** > **+ Create**  
3. Fill in:
   - Subscription: Default  
   - Name: `OnPrem-Lab`  
   - Region: (e.g., `West US`)  
4. Click **Review + Create** > **Create**

---

### STEP 2: Set Up the Virtual Network (VNet)

1. Search: **Virtual networks** > **+ Create**  
2. Configuration:
   - Name: `OnPremVNet`  
   - Region: same as resource group  
   - Address space: `10.0.0.0/16`  
3. Add Subnet:
   - Name: `default`  
   - Subnet range: `10.0.0.0/24`  
4. **Review + Create** > **Create**

---

### STEP 3: Create Network Security Group (NSG)

1. Search: **Network security groups** > **+ Create**  
2. Fill in:
   - Name: `OnPrem-NSG`  
   - Region: same as above  
3. Click **Review + Create** > **Create**

#### Add Inbound Rules:

**Allow RDP (TCP 3389):**

| Setting           | Value       |
|------------------|-------------|
| Source           | Any         |
| Destination Port | 3389        |
| Protocol         | TCP         |
| Action           | Allow       |
| Priority         | 100         |
| Name             | Allow-RDP   |

**Allow ICMP (Ping):**

| Setting           | Value        |
|------------------|--------------|
| Protocol         | ICMP         |
| Action           | Allow        |
| Priority         | 110          |
| Name             | Allow-ICMPv4 |

---

### STEP 4: Deploy Domain Controller (Windows Server 2022)

1. Go to **Virtual Machines** > **+ Create**  
2. Configuration:
   - Name: `DC-Server`  
   - Image: `Windows Server 2022 Datacenter – Gen2`  
   - Size: `B1s`  
   - Username: `labadmin`  
   - Password: Strong password  
   - Inbound Ports: **RDP (3389)**  
3. Disk: **Standard HDD**
4. Networking:
   - VNet: `OnPremVNet`  
   - Subnet: `default`  
   - NSG: Select `OnPrem-NSG` or reassign later  
5. Click **Review + Create** > **Create**

---

### STEP 5: Deploy Client PC (Windows 10 Pro)

Same steps as above with these differences:

- Name: `Client-PC`  
- Image: `Windows 10 Pro, Version 22H2 – Gen2`  
- **Confirm license** checkbox for multi-tenant hosting  
- Assign same VNet/Subnet and NSG

---

### STEP 6: Connect via RDP

1. Open Azure Portal > on both VMs > **Connect > RDP**  
2. Download `.rdp` file and open  
3. Enter:
   - Username: `labadmin`  
   - Password: the one you set  
4. If prompted with a security warning, click Yes to proceed
5. Inside one VM, open Command Prompt, type: `ipconfig`
   Copy the IPv4 address from this output — this is the private IP of the VM.
6. Go to the second VM, open Command Prompt, and type: `ping [PRIVATE IP of the first VM]`
   - Example: `ping 10.0.0.5`
   - You should see successful replies (4 packets sent/received).

**Always ping the private IP,** not the public one, since both VMs are on the same internal virtual network.

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
