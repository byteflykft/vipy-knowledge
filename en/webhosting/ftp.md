# Using FTP

With **FTP**, you can easily upload, edit, and delete files from your hosting account.

## Steps

### 1. Create an account

:::warning It is important to choose a long, secure password to protect your website.:::

1. Log in to your hosting control panel and click on **FTP Users** in the left-hand menu, then click on **Add FTP User**.
2. Enter a username and password of your choice.
3. For the directory, enter the path you want the user to have access to. By default, you can leave it unchanged.
4. Finally, click the save button, and your FTP account will be created.

### 2. Connect
1. Download an FTP client, such as [WinSCP](https://winscp.net/eng/download.php)
2. After launching the program, fill out the fields as shown in the image and click the **Login** button.

- Protocol: `FTP`
- Encryption: `TLS/SSL explicit encryption`
- Hostname: your web hosting server, e.g. `kw1.vipy.hu`
- Port: `21`
- Username: `the one provided when creating the FTP account`
- Password: `the one provided when creating the FTP account`

![WinSCP Connect](https://raw.githubusercontent.com/byteflykft/vipy-knowledge/refs/heads/knowledge/en/webhosting/images/winscp-connect.png)

### 3. Success
After a successful login, you can manage your hosting files freely.
