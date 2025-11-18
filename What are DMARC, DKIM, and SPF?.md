# What are DMARC, DKIM, and SPF?
SPF, DKIM, and DMARC help authenticate email senders by verifying that the emails came from the domain that they claim to be from. These three authentication methods are important for preventing spam, phishing attacks, and other email security risks.




## What is SPF?
SPF (Sender Policy Framework) is an email authentication protocol that helps prevent email spoofing by verifying whether a message was sent from an authorized mail server for a given domain. It works by checking DNS records to confirm if the sending server is permitted to send emails on behalf of that domain
  - **Purpose:** Protects against spoofing, phishing, and spam by ensuring emails claiming to be from a domain are actually sent by approved servers
- **Mechanism:** SPF uses DNS TXT records where domain owners publish a list of authorized mail servers.

### How does SPF work?
1. Domain Owner Setup
   - The domain administrator creates an SPF record in DNS.
   - This record specifies which IP addresses or mail servers are allowed to send emails for that domain.
3. Email Sent
   - When an email is sent, the receiving mail server checks the domain in the “From” address.
5. SPF Record Lookup
   - The receiving server queries the DNS for the SPF record of that domain.
7. Verification
   - The server compares the sending IP address against the list of authorized servers in the SPF record.
9. Result
    - If the IP matches → Pass (email is considered legitimate).
    - If not → Fail (email may be marked as spam, rejected, or flagged depending on policies).

## What is DKIM?
### How does DKIM work?

## What is DMARC?
### How does DMARC work?


## Where are SPF, DKIM, and DMARC records stored?
SPF, DKIM, and DMARC records are stored in the **Domain Name System (DNS)**, which is publicly available. The DNS's main use is matching web addresses to IP addresses, so that computers can find the correct servers for loading content over the Internet without human users having to memorize long alphanumeric addresses.

**The DNS can also store a variety of records associated with a domain, including alternate names for that domain**
-  Domain (CNAME records),
-  IPv6 addresses (AAAA records),
-  And reverse DNS records for domain lookups (PTR records).

DKIM, SPF, and DMARC records are all stored as **DNS TXT records**. A DNS TXT record stores text that a domain owner wants to associate with the domain. This record can be used in a variety of ways, since it can contain any arbitrary text. DKIM, SPF, and DMARC are three of several applications for DNS TXT records.

## How to set up DMARC, DKIM, and SPF for a domain
To set up DMARC, DKIM, and SPF for your domain, you need to add specific DNS TXT records that define how email servers should authenticate messages sent from your domain. These records are configured through your domain's DNS provider (like Cloudflare, GoDaddy, etc.).
### Setup Guide
1. **SPF (Sender Policy Framework)**
   - **Purpose:** Lists which servers are allowed to send emails for your domain.
   - **Record Type:** TXT
   - **Example Value:**
     ```
     v=spf1 include:your-email-provider.com -all
     ```
   - **Steps:**
     - Log in to your DNS provider.
     - Add a new TXT record for your domain.
     - Set the name to `@` or your domain name.
     - Paste the SPF value provided by your email service (e.g., Google Workspace, Microsoft 365).

2. **DKIM (DomainKeys Identified Mail)**
   - **Purpose:** Adds a digital signature to your emails to verify they weren’t altered.
   - **Record Type:** TXT (sometimes CNAME depending on provider)
   - **Steps:**
     - Go to your email provider’s admin console.
     - Generate DKIM keys (public/private).
     - Copy the public key and selector name.
     - In your DNS provider, add a TXT record:
       - Name: `selector._domainkey.yourdomain.com`
       - Value: The public key string

3. **DMARC (Domain-based Message Authentication, Reporting & Conformance)**
   - **Purpose:** Tells receiving servers what to do if SPF or DKIM checks fail.
   - **Record Type:** TXT
   - **Example Value:**
     ```
     v=DMARC1; p=reject; rua=mailto:dmarc-reports@yourdomain.com
     ```
   - **Steps:**
     - Add a TXT record in DNS.
     - Name: `_dmarc.yourdomain.com`
     - Value: Choose a policy:
       - `p=none` → Monitor only
       - `p=quarantine` → Send suspicious emails to spam
       - `p=reject` → Block unauthenticated emails
     - Add rua to receive reports.

### Best Practices
- Start with `p=none` for DMARC to monitor before enforcing.
- Use `-all` in SPF to strictly block unauthorized senders.
- Rotate DKIM keys periodically for security.
- Check records with tools like MXToolbox or DMARC Analyzer.

> **Note:** They work together to protect your domain from spoofing, phishing, and spam.

## How to check if an email has passed SPF, DKIM, and DMARC
DMARC, DKIM, and SPF have to be set up in the domain's DNS settings. Administrators can contact their DNS provider — or, their web hosting platform may provide a tool that enables them to upload and edit DNS records. For more details on how these records work, see our articles about them:
