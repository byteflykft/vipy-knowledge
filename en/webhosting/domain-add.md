# Domain Management

## Creating / Editing a Domain
- Log in to your hosting control panel and click on **Domains** in the left-hand menu, then click **Add Domain**.

### General
- Full domain name: enter the domain name you purchased and can manage. If you enter a domain that doesn’t belong to you, your website will not work.
- WWW subdomain: recommended — this ensures that `www.domain.tld` also loads your website by default.
- Domain target → Local directory: this will be the main folder for the domain where your website files are located.
- Domain target → Redirect: enter the target address for redirection and select the redirect type. The `302` code is recommended as it does not harm your domain’s SEO. Use `301` only if the domain will no longer be used.
- Disable DNS: check this box if you prefer to manage your DNS records with an external provider (e.g., Cloudflare).

### Security
- SSL/TLS certificate: it is recommended to choose **Let’s Encrypt**, which automatically installs a free SSL certificate.
- Force HTTPS: automatically redirects visitors from `http://` to `https://`.
- HSTS: also recommended — it informs browsers that the site is only accessible through a secure connection.
- Subdomains: applies the same SSL/HSTS settings to all existing subdomains.

### Interpreter
- PHP interpreter: determines the PHP version your website will use.
- Apply PHP interpreter to all subdomains: useful when editing; if checked and saved, this PHP version will apply to all subdomains as well.

## Nameserver Configuration

### Managed by VIPY Hosting
- In this case, set the nameservers provided in your client portal for your domain. This must be done at your domain registrar.
- Note that DNS changes may take several hours to propagate globally.

### Managed by External Provider
- If you prefer to manage your DNS externally, create and configure your records there using the nameservers they provide.
- You will need to manually create the required records for your hosting (e.g., A, CNAME, MX, etc.).
- Email-related configuration details can be found under **Client Portal** → **Email**.
