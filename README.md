<p align="center">
  <img src="https://github.com/jorge-nbb/images/raw/main/ad1.png" alt="Active Directory" width="300"/>
</p>

<h1 align="center">Configure Active Directory and Domain Join</h1>

---

## 🎯 Project Purpose

- Simulate an on-premises Active Directory environment inside Azure.  
- Deploy and configure Active Directory Domain Services (AD DS), promote a domain controller, and join a client to the domain.

---

## 🛠️ Tools & Services Used

- **Windows Server 2022 (DC-Server)**
- **Windows 10 Pro (Client-PC)**
- **Active Directory Domain Services**
- **Azure Virtual Network**
- **Remote Desktop Protocol (RDP)**

---

## ⚙️ Configuration Steps

### STEP 1: Install Active Directory Domain Services (AD DS)

<table>
  <tr>
    <td style="vertical-align: top; width: 60%;">

On **DC-Server** (Windows Server 2022 VM):

1. Click Start > search for **Server Manager** and open it  
2. Click **Add roles and features**  
3. Click **Next** until you reach **Server Roles**  
4. Check **Active Directory Domain Services**  
5. Click **Add Features** if prompted  
6. Continue clicking **Next** > then click **Install**  
7. Wait for the installation to complete (do not close the wizard)

</td>
<td style="vertical-align: top; text-align: right; width: 40%;">
  <img src="https://github.com/jorge-nbb/images/raw/main/p24.png" alt="24" width="350"/>
  <hr>
  <img src="https://github.com/jorge-nbb/images/raw/main/p25.png" alt="25" width="350"/>
</td>
  </tr>
</table>

---

### STEP 2: Promote the Server to a Domain Controller

<table>
  <tr>
    <td style="vertical-align: top; width: 60%;">

1. In **Server Manager**, click the yellow warning flag (top-right)  
2. Click **Promote this server to a domain controller**  
3. Choose: **Add a new forest**
   - Root domain name: `adminlab.local`  
5. Set a DSRM password (can be same as your login)  
6. Click **Next** through defaults, then **Install**  
7. Server will reboot once promotion completes

</td>
<td style="vertical-align: top; text-align: right; width: 40%;">
  <img src="https://github.com/jorge-nbb/images/raw/main/p26.png" alt="26" width="350"/>
</td>
  </tr>
</table>

---

### STEP 3: Verify Domain Setup

<table>
  <tr>
    <td style="vertical-align: top; width: 60%;">

1. After reboot, log back into DC-Server using `adminlab`  
2. Open **Server Manager** > **Tools** > **Active Directory Users and Computers**  
3. Confirm domain appears on left panel: `adminlab.local`

</td>
<td style="vertical-align: top; text-align: right; width: 40%;">
  <img src="https://github.com/jorge-nbb/images/raw/main/p28.png" alt="28" width="350"/>
</td>
  </tr>
</table>

---

### STEP 4: Create a Domain User

<table>
  <tr>
    <td style="vertical-align: top; width: 60%;">

1. In **Active Directory Users and Computers**, expand `adminlab.local`  
2. Right-click **Users** > **New** > **User**  
3. Create test user:  
   - First name: `Test`  
   - Last name: `User`  
   - Logon name: `testuser`  
   - Password: secure but simple  
   - Uncheck **User must change password at next login**  
4. Click **Next** > **Finish**

</td>
<td style="vertical-align: top; text-align: right; width: 40%;">
 <img src="https://github.com/jorge-nbb/images/raw/main/p29.png" alt="29" width="350"/>
</td>
  </tr>
</table>

---

### STEP 5: Join Windows 10 Client to the Domain

<table>
  <tr>
    <td style="vertical-align: top; width: 60%;">

On **Client-PC (Windows 10 Pro VM):**

1. Go to **Settings > System > About**  
2. Click **Rename this PC (advanced)** > then click **Change**  
3. Under **Member of**, select **Domain** and enter: `adminlab.local`
4. **Important** - Configure DNS First:
   - Open Control Panel > Network and Internet > Network and Sharing Center
   - Click **Change adapter settings**  
   - Right-click **Ethernet > Properties**  
   - Select **Internet Protocol Version 4 (TCP/IPv4)** > click **Properties**  
   - Under **Preferred DNS server**, enter private IP of **DC-Server** (e.g. `10.0.0.4`)  
   - Click **OK > OK**  
5. Open Command Prompt and test with:
```sh
ping adminlab.local
```
- You should see replies from DC’s private IP.

6. Now join the domain:

- Domain: `adminlab.local`  
- Username: `Adminlab`  
- Password: your DC password  
- Click **OK** > then **Restart Now**

</td>
<td style="vertical-align: top; text-align: right; width: 40%;">
  <img src="https://github.com/jorge-nbb/images/raw/main/p30.png" alt="30" width="350"/>
  <hr>
  <img src="https://github.com/jorge-nbb/images/raw/main/p31.png" alt="31" width="350"/>
   <hr>
  <img src="https://github.com/jorge-nbb/images/raw/main/p33.png" alt="33" width="350"/>
</td>
  </tr>
</table>

---

### STEP 6: Log In with Domain User

<table>
  <tr>
    <td style="vertical-align: top; width: 60%;">

By default, RDP logs you into the last account used. To test domain logins:

#### ✅ Use MSTSC to Log in as a Domain User

1. On your **local PC**, press `Windows + R` > type `mstsc`  
2. Click **Show Options**  
3. In **Computer**, enter public IP of Client VM  
4. In **Username**, enter:  
   ```
   adminlab\testuser
   ```
5. Click **Connect**  
6. Enter password for `testuser`

#### 🧱 Important: Add Domain User to Remote Desktop Group

If you get a login error:

1. Inside Client VM, press `Windows + R` > type:  
   ```
   sysdm.cpl
   ```
2. Go to **Remote** tab > click **Select Users**  
3. Click **Add** > type:  
   ```
   adminlab\testuser
   ```
4. Click **Check Names** > **OK**

You can now RDP as `adminlab\testuser`.

</td>
<td style="vertical-align: top; text-align: right; width: 40%;">
  <img src="https://github.com/jorge-nbb/images/blob/main/p34.1.png" alt="34.1" width="350"/>
  <hr>
  <img src="https://github.com/jorge-nbb/images/blob/main/p35.png" alt="35" width="350"/>
</td>
  </tr>
</table>

---

## 📝 Results

- Domain Controller successfully promoted and configured  
- Client PC joined to `adminlab.local` domain  
- Domain users can now log in via RDP  
- DNS and internal name resolution successfully configured

Azure-hosted Active Directory lab has been created.

---

