# Windows RDP WireGuard

## Installation on the VPS

### 1. Install WireGuard
- [Click here](https://download.wireguard.com/windows-client/wireguard-installer.exe) to download and install it.

### 2. Create configuration
- On [this page](https://dbca-wa.github.io/wg-webcfg/wg-webcfg.html), fill in the **Server** field with the VPS IP address, and if you need multiple clients, increase the number of clients.
- Then click the **Generate** button.

### 3. Configure WireGuard
- After opening WireGuard, click the arrow next to **Add empty tunnel** and select **Add empty tunnel**, then enter **VPS** as the name and paste the server configuration into the large text box.
- Then click **Save**, and the VPN will start.

### 4. Configure the Firewall
- Open the **Windows Defender Firewall with Advanced Security** application and go to the **Inbound Rules** section.
- Find the rules named **Remote Desktop - User Mode (TCP-In)** and **Remote Desktop - User Mode (UDP-In)** (Port: 3389).
- Click on the first one, then open the **Scope** tab and under **Remote IP address**, select **These IP addresses**, then **Add...**, enter `172.17.172.0/24`, and click **Apply** and **OK**.
- Repeat the same for the other rule, and you’re done.

## Installation on your local computer

### 1. Install WireGuard
- [Click here](https://download.wireguard.com/windows-client/wireguard-installer.exe) to download and install it.

### 2. Configure WireGuard
- After opening WireGuard, click the arrow next to **Add empty tunnel** and select **Add empty tunnel**, then enter **VPS** as the name and paste one of the client configurations into the large text box.
- Then click **Save**, and if the setup is correct, the connection will become active.

### 3. Connect via RDP
- Open the **Remote Desktop Connection** application and, by default, connect to `172.17.172.1`. (Provided that the server configuration includes `Address = 172.17.172.1/24`.)
