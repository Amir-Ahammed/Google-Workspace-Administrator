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

---

## What is DKIM?
DKIM (DomainKeys Identified Mail) is an email security protocol that uses cryptographic signatures to prove that an email was sent by an authorized sender and hasn’t been tampered with. It works by attaching a digital signature to each outgoing email, which receiving servers can verify using public keys stored in DNS.
- **DKIM = DomainKeys Identified Mail**
- It’s like a **digital seal** of **authenticity** for your emails.
- It helps prevent **email spoofing**, **phishing**, and **tampering**.

### How does DKIM work?
1. Key Generation
   - Your email system generates a private/public key pair.
   - The private key is used to sign outgoing emails.
   - The public key is published in your domain’s DNS as a TXT record.
3. Email Signing
   - When you send an email, your server uses the private key to create a DKIM signature.
   - This signature is added to the email header (invisible to users).
4. Email Receiving
   - The receiving server sees the DKIM signature and checks your DNS for the public key.
   - It uses the public key to verify that:
     - The email was really sent by your domain.
     - The message wasn’t altered during transit.
5. Result
   - If the signature matches → Email is trusted.
   - If it fails → Email may be flagged, quarantined, or rejected (especially if DMARC is also set).

### DKIM Example

```
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/simple;
 d=example.com;
 s=selector1; t=1527769943;
 bh=Vn1MqIIvwm8bUx8kZsHrc9bCpNEK+jIxSz7UkTaBcU0=;
 h=From:To:Subject:Date:Message-ID;
 b=Rjr1D+kr2HgpA8n0sHxywIDzi5no7vV2Ug3qog7ebJs0/KGz10WTfbwXqch+xHnZ

```
**Field-by-field Explanation**
- `v=1` → DKIM version.
- `a=rsa-sha256`
  - **rsa** → public/private key signing
  - **sha256** → hashing algorithm
- `c=relaxed/simple`
  - **relaxed** → headers ignore minor formatting differences (extra spaces, line breaks)
  - **simple** → body is strict and sensitive to formatting changes
- `d=example.com`
  - The **domain** that signed the email.
  - The receiver will look up this domain's DNS to retrieve the **public key**.
- `s=selector1`
  - The **selector** used to locate the DKIM public key in DNS.
    - The DNS record will be queried at:
      ```
      selector1._domainkey.example.com
      ```
- `t=1527769943`
  - Timestamp (Unix epoch time) when the email was signed.
  - Helps detect old or replayed signatures.
- `bh=Vn1MqIIvwm8bUx8kZsHrc9bCpNEK+jIxSz7UkTaBcU0=`
  - **Body** hash of the email.
  - Ensures the email body was not modified after signing.
  - A SHA-256 hash of the canonicalized body.
- `h=From:To:Subject:Date:Message-ID`
  - List of headers included in the **DKIM signature**.
  - If **any** of these headers are altered, DKIM verification will fail.
- `b=Rjr1D+kr2HgpA8n0sHxywIDzi5no7vV2Ug3qog7ebJs0/KGz10WTfbwXqch+xHnZ`
  - The actual **cryptographic signature**.
  - Generated using the sender’s **private key**.
  - The receiving server uses the **public key from DNS** to verify this value.

---

## What is DMARC?
DMARC (Domain-based Message Authentication, Reporting & Conformance) is an email security protocol that protects your domain from spoofing and phishing by telling receiving servers how to handle emails that fail SPF or DKIM checks. It works by publishing a policy in DNS that enforces authentication and provides reporting.

**Purpose:** It helps prevent email spoofing, phishing, and fraud 

- **DMARC = Domain-based Message Authentication, Reporting & Conformance**
- It builds on **SPF** and **DKIM** to give domain owners control over unauthenticated emails.
- It helps prevent attackers from sending fake emails using your domain.

### How does DMARC work?
1. You Publish a DMARC Policy
   - You add a TXT record in your domain’s DNS.
   - This record tells email servers:
     - What to do if SPF or DKIM fails.
     - Where to send reports about email activity.
3. Email Gets Sent
   - The receiving server checks:
     - Does the email pass SPF?
     - Does it pass DKIM?
     - Does the “From” address align with those checks?
5. DMARC Applies Your Policy
   - If checks fail, the server follows your DMARC instructions:
     - `p=none` → Just monitor (no action).
     - `p=quarantine` → Send to spam/junk.
     - `p=reject` → Block the email.
7. You Receive Reports
   - DMARC sends aggregate reports to the email address you specify (via `rua=`).
   - These reports show who’s sending emails using your domain and whether they passed authentication.

#### Example DMARC Record
```
Name: _dmarc.example.com  
Value: v=DMARC1; p=reject; rua=mailto:dmarc-reports@example.com

```
---

## Where are SPF, DKIM, and DMARC records stored?
SPF, DKIM, and DMARC records are stored in the **Domain Name System (DNS)**, which is publicly available. The DNS's main use is matching web addresses to IP addresses, so that computers can find the correct servers for loading content over the Internet without human users having to memorize long alphanumeric addresses.

**The DNS can also store a variety of records associated with a domain, including alternate names for that domain**
-  Domain (CNAME records),
-  IPv6 addresses (AAAA records),
-  And reverse DNS records for domain lookups (PTR records).

DKIM, SPF, and DMARC records are all stored as **DNS TXT records**. A DNS TXT record stores text that a domain owner wants to associate with the domain. This record can be used in a variety of ways, since it can contain any arbitrary text. DKIM, SPF, and DMARC are three of several applications for DNS TXT records.

---

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
   - **Example Value:**
     ```
     Name: selector1._domainkey.example.com
     Value: v=DKIM1; k=rsa; p=MIGfMA0GCSqGSIb3DQEBA...
     ```
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

---

## How to check if an email has passed SPF, DKIM, and DMARC
DMARC, DKIM, and SPF have to be set up in the domain's DNS settings. Administrators can contact their DNS provider — or, their web hosting platform may provide a tool that enables them to upload and edit DNS records. For more details on how these records work, see our articles about them:
