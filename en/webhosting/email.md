# Email

## Prerequisites
- A working domain added to your hosting and DNS manager (or using an external DNS provider)
- Detailed setup instructions can be found under **Client Portal** -> **Email**.

:::info To ensure reliable email delivery, make sure all records are set up correctly.:::

### 1. MX Record
- This record is required to route emails for your domain to the correct mail server. Use this host as your mail server.
- If you use the built-in DNS manager, this record will be created automatically.
- Record type: `MX`
- Hostname: `@`
- Value: your hosting mail server, e.g. `kw1.vipy.hu`

### 2. DKIM Record
- If you use the built-in DNS manager, this record is created automatically.
- DKIM (DomainKeys Identified Mail) verifies that emails were sent from your domain and not altered in transit. It’s strongly recommended for better email security and deliverability.

### 3. DMARC Record
- DMARC (Domain-based Message Authentication, Reporting, and Conformance) helps protect your domain from email spoofing and provides reports on authentication results.
- Record type: `TXT`
- Hostname: `_dmarc`
- Value: `v=DMARC1; p=none; sp=none; adkim=r; aspf=r;`

### 4. SPF Record
- SPF (Sender Policy Framework) helps receiving mail servers verify that your messages come from authorized sources.
- Record type: `TXT`
- Hostname: `@`
- Value: `v=spf1 include:mxroute.com -all`
- To use multiple email services (e.g., for newsletters), combine them like this:  
  `v=spf1 include:mxroute.com include:other-service.com -all`

## Creating an Email Account

:::warning Choose a long, secure password to keep your email account safe.:::

1. Log in to your hosting control panel and click **Email Accounts**, then click **Add Email Account**.
2. Enter your desired email address and select which domain it should belong to.

- Quota: if your mailbox reaches this limit, you will not be able to send or receive emails.
- Catch-all: receives all emails sent to non-existent addresses within the domain.
- Aliases: additional addresses that forward to the main mailbox.
- Forwarding: automatically forwards all incoming emails to one or more addresses.
- Advanced settings: do **not disable** antivirus or spam protection; it is strongly recommended to keep them enabled.
- Auto-delete old spam/trash: recommended to save disk space.

## Using Your Email

### 1. Through Roundcube
- Click the mail icon in the actions column to open your webmail inbox.

### 2. Add to Gmail or Other Clients
- Under “Connection details”, you can find all information required to connect your mailbox to email clients.

## Notes

- Using the hosting mail service for **spam or newsletter sending** is strictly prohibited. For more details, review the [General Terms and Conditions](https://1sw3fse.vipy.hu/vipy/web/docs/ASZF.pdf).
- Our goal is reliable and successful email delivery.
