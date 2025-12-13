# Remote Connection to a Server (Windows RDP and Linux SSH)

This guide explains how to connect

* to a **Windows server via RDP**, and
* to a **Linux server via SSH**

from **Windows**, using either **PuTTY** or the **Command Prompt (CMD / PowerShell)**,
and also how **SSH key-based authentication** works if it was configured **during the server rental process**.


## Connecting to a Windows Server via RDP

### 1. Opening Remote Desktop Connection

* Press **Windows + R**
* Type:

  ```
  mstsc
  ```
* Press **Enter**

**or on an English Windows system**

* Press the **Windows** key
* Type:

  ```
  Remote Desktop Connection
  ```
* Press **Enter**



### 2. Entering Connection Details

* In the **Computer** field, enter:

  * the server **IP address** (e.g. `172.17.172.1`)
  * *(Recommended: [WireGuard setup on VPS](https://vipy.hu/en/article/windows-rdp-wireguard))*
* Click **Connect**



### 3. Authentication

* Enter:

  * **Username:** `Administrator`
  * **Password:** the password received via email
* After successful authentication, the server desktop will appear

:::info ℹ️ If a certificate warning appears, click **Yes** to continue. :::



### 4. Common Issues

* **The computer can’t be reached**
  → Check whether the virtual machine is running
  → Verify that RDP (TCP port 3389) is enabled
* **Access denied**
  → Verify user permissions



## Connecting to a Linux Server via SSH (PuTTY)

### 1. Downloading PuTTY

* Download from:
  [https://www.putty.org/](https://www.putty.org/)
* After installation, start the **PuTTY** application



### 2. Entering Connection Details

In the **Session** menu:

* **Host Name (or IP address):**

  ```
  <VPS_IP>
  ```
* **Port:**

  ```
  22
  ```
* **Connection type:**

  ```
  SSH
  ```

Click **Open**.



### 3. Login (Password-based)

* **login as:** → username (e.g. `root`)
* **password:** → password

:::warning ⚠️ No characters will appear while typing the password — this is normal. :::



## Connecting to a Linux Server via SSH (CMD / PowerShell)

### 1. Opening Command Prompt or PowerShell

* **CMD**:

  * Start → `cmd`
* **PowerShell**:

  * Start → `powershell`



### 2. Using SSH (Password-based)

```bash
ssh root@<VPS_IP>
```



### 3. Confirming First Connection

On the first connection, you may see:

```
Are you sure you want to continue connecting (yes/no)?
```

Type:

```
yes
```

Then enter the password.



## Connecting to a Linux Server Using SSH Key Authentication (Passwordless)

If an **SSH public key was provided during the server rental process**, the server will **not ask for a password** and will authenticate automatically using the key.



### 1. SSH Key Authentication via CMD / PowerShell

If the private key is stored in the default location (`~/.ssh/id_rsa`):

```bash
ssh root@<VPS_IP>
```

If you are using a **custom private key file**:

```bash
ssh -i C:\Users\Username\.ssh\vipy_key root@<VPS_IP>
```

With correct configuration, **no password will be requested**.



### 2. SSH Key Authentication Using PuTTY

When using PuTTY, the key must be in **PPK format**.

#### Steps:

1. Start **PuTTY**
2. Navigate to:

   ```
   Connection → SSH → Auth → Credentials
   ```
3. In **Private key file for authentication**:

   * select the `.ppk` private key file
4. Return to the **Session** menu
5. Set:

   * Host Name
   * Port (22)
   * SSH
6. Click **Open**

If configured correctly, you will be logged in **without being prompted for a password**.



### 3. Important Notes About SSH Key Authentication

* The key is **installed during the server rental process**
* Only clients that possess the **private key** can connect
* If the key is lost, a new key must be uploaded via the control panel or directly on the system
* **Multiple SSH keys** can be associated with a single server



## Common SSH Errors

* **Connection refused**

  * SSH service is not running
  * Incorrect port is being used
* **Permission denied (publickey)**

  * Incorrect or missing SSH key
  * Wrong username
  * Invalid or missing private key
* **No route to host**

  * VPN / WireGuard is not active
  * Network connectivity is missing


