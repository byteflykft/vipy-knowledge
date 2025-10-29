# SSH Access

**SSH** access can be very useful for running commands such as composer, php artisan, or git. Learn how to connect to your VIPY web hosting account via SSH.

## Connection

### 1. SSH Application
- It is recommended to use a convenient application like [Termius](https://termius.com/), but you can also use [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) or connect directly from a terminal.
- In this guide, we include instructions for Putty, but the process is similar across all clients.

### 2. Connection
- Open the Putty application after installation and enter your hosting server’s address in the **Hostname** field, e.g. `kw1.vipy.hu`. Keep the default port `22`, then click **Open**.

:::warning Never share your password with others. If necessary, change it immediately for security.:::

![Putty Open](https://raw.githubusercontent.com/byteflykft/vipy-knowledge/refs/heads/knowledge/hu/webhosting/images/putty.png)

- For the username, enter your account name as shown in the client portal, e.g. `client1`.
- The password is the same as your hosting account password. If you change it in the panel, it will also change for SSH access.

:::danger Each hosting account has only one SSH user, which has full access to all files on your web hosting space.:::

## Available commands

### Useful

#### PHP, Composer

The PHP versions selectable in the control panel are also available to run via SSH. If you want to run composer with a specific PHP version, you can do it as follows:

```bash
keyhelp-php82 /usr/local/bin/composer <...>
```

In this case it will be executed with **PHP 8.2**. The `php` command always uses the newest version.

### All

#### PHP

```bash
keyhelp-php74
keyhelp-php80
keyhelp-php81
keyhelp-php82
keyhelp-php83
keyhelp-php84
php
composer
```

#### Transfer:

```bash
git
scp
rsync
wget
curl
git-upload-archive
git-receive-pack
git-upload-pack
```

#### File management:

```bash
mkdir
rm
rmdir
cd
ls
ln
cp
mv
unzip
gunzip
gzip
tar
vi
nano
```

#### Bash:

```bash
cat
echo
exec
head
less
clear
exit
logout
help
typeset
bind
chmod
dirs
export
grep
history
source
env
touch
git-shell
whoami
alias
du
kill
```
