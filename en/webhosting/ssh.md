# SSH Access

**SSH** access can be very useful for running commands such as composer, php artisan, or git. Learn how to connect to your VIPY web hosting account via SSH.

## 1. SSH Application
- It is recommended to use a convenient application like [Termius](https://termius.com/), but you can also use [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) or connect directly from a terminal.
- In this guide, we include instructions for Putty, but the process is similar across all clients.

## 2. Connection
- Open the Putty application after installation and enter your hosting server’s address in the **Hostname** field, e.g. `kw1.vipy.hu`. Keep the default port `22`, then click **Open**.

:::warning Never share your password with others. If necessary, change it immediately for security.:::

![Putty Open](https://raw.githubusercontent.com/byteflykft/vipy-knowledge/refs/heads/knowledge/hu/webhosting/images/putty.png)

- For the username, enter your account name as shown in the client portal, e.g. `client1`.
- The password is the same as your hosting account password. If you change it in the panel, it will also change for SSH access.

:::danger Each hosting account has only one SSH user, which has full access to all files on your web hosting space.:::
