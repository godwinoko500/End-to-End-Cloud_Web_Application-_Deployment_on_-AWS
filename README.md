# End-to-End Cloud Web Application Deployment on AWS

A complete, production-style cloud deployment demonstrating custom AWS VPC networking, EC2 compute provisioning, custom DNS routing, Nginx web server configuration, SSL/TLS encryption via Let's Encrypt, and external penetration testing.

**Live Application:** [https://zeltechhub.shop](https://zeltechhub.store)

---

## 🏗️ Architecture Overview

```text
+-----------------------------------------------------------------------+

|                           AWS Cloud (Region)                          |
|                                                                       |
|   +---------------------------------------------------------------+   |
|   |                  Custom VPC (192.168.0.0/16)                  |   |
|   |               [ VPC Flow Logs ---> S3 Bucket ]                |   |
|   |                                                               |   |
|   |   +-------------------------------------------------------+   |   |
|   |   |            Public Subnet (192.168.10.0/24)            |   |   |
|   |   |                                                       |   |   |
|   |   |   +-----------------------------------------------+   |   |   |
|   |   |   |           EC2 Instance (Ubuntu 20GB)           |   |   |   |
|   |   |   |     Security Group: SSH (22), HTTP (80),      |   |   |   |
|   |   |   |                  HTTPS (443)                  |   |   |   |
|   |   |   |      [ Nginx Web Server + Let's Encrypt ]     |   |   |   |
|   |   |   +-----------------------------------------------+   |   |   |
|   |   +-------------------------------------------------------+   |   |
|   |                               |                               |   |
|   |                      [ Route Table: 0.0.0.0/0 ]               |   |
|   +-------------------------------|-------------------------------+   |
|                                   |                                   |
|                        [ Internet Gateway (IGW) ]                     |
+-----------------------------------|-----------------------------------+
                                    |
                             (Public Internet)
                                    |
                   [ DNS A-Record: zeltechhub.shop ]
```

---

## 🛠️ Tech Stack & Tools
* **Cloud Provider:** Amazon Web Services (VPC, EC2, S3, Security Groups, VPC Flow Logs)
* **Operating System:** Ubuntu Linux
* **Web Server:** Nginx
* **Security & SSL/TLS:** Certbot & Let's Encrypt
* **SSH & File Transfer:** MobaXterm
* **DNS & Validation:** Domain Registrar DNS + [DNSChecker.org](https://dnschecker.org)
* **Security Auditing:** [Pentest-Tools.com](https://pentest-tools.com)

---

## 🚀 Deployment Phases

### Phase 1: AWS Network Infrastructure & Monitoring
1. **Virtual Private Cloud (VPC):** Created a custom VPC (`Zl-t-vpc`) with CIDR block `192.168.0.0/16`.
2. **Subnet:** Provisioned a public subnet (`zl-t-Subnet`) with CIDR block `192.168.10.0/24`.
3. **Internet Gateway & Routing:**
   * Created an Internet Gateway (`zl-t-IGW`) and attached it to `Zl-t-vpc`.
   * Associated `zl-t-Subnet` with a custom Route Table containing a default route (`0.0.0.0/0`) targeting `zl-t-IGW`.
4. **Security Group (Ingress Rules):** Configured firewall rules allowing inbound traffic on:
   * **SSH (Port 22):** Administrative access
   * **HTTP (Port 80):** Web traffic
   * **HTTPS (Port 443):** Encrypted TLS traffic
5. **VPC Flow Logs & S3 Storage:** Created an Amazon S3 bucket and enabled VPC Flow Logs to capture and audit network traffic metadata.

---

### Phase 2: EC2 Compute Provisioning
1. Launched an Ubuntu EC2 instance (`t2.micro` — 1 vCPU, 1 GB RAM / `t2.medium` — 2 vCPU, 4 GB RAM).
2. Configured **20 GB** of EBS root storage.
3. Attached the instance to `Zl-t-vpc` and `zl-t-Subnet` with **Auto-assign Public IP** enabled.
4. Attached the custom Security Group and generated a `.pem` SSH key pair for secure remote access.

---

### Phase 3: DNS Record Configuration
1. Registered the custom domain (`zeltechhub.store`).
2. Created a DNS **A Record** pointing `zeltechhub.store` to the EC2 instance's Public IPv4 address.

---

### Phase 4: Web Content Preparation
1. Downloaded a responsive web template from **ThemeWagon**.
2. Customized the HTML/CSS assets locally for the project (`zeltech`) and compressed the directory into a `.zip` archive.

---

### Phase 5: File Transfer via MobaXterm
1. Connected to the EC2 instance via SSH using MobaXterm and the `.pem` private key.
2. Created a staging directory and uploaded the `.zip` archive via MobaXterm's SFTP sidebar:
   ```bash
   mkdir webcontent
   ```
3. Installed `unzip` and extracted the website files:
   ```bash
   sudo apt update -y
   sudo apt install unzip -y
   unzip webcontent/<template-archive>.zip -d webcontent/
   ```

---

### Phase 6: Nginx Web Server Installation & Deployment
1. Updated system packages and installed Nginx:
   ```bash
   sudo apt update -y && sudo apt upgrade -y
   sudo apt install nginx -y
   ```
2. Verified and enabled the Nginx service:
   ```bash
   sudo systemctl enable nginx
   sudo systemctl start nginx
   sudo systemctl status nginx
   ```
3. Cleared the default Nginx landing page and deployed the custom web files to the web root (`/var/www/html/`):
   ```bash
   sudo rm -rf /var/www/html/*
   sudo cp -rf webcontent/mihaltech/* /var/www/html/
   sudo systemctl restart nginx
   ```
4. Verified that the site loaded properly via the EC2 Public IP address in a web browser.

---

### Phase 7: Global DNS Propagation Verification
1. Navigated to [DNSChecker.org](https://dnschecker.org).
2. Queried the **A Record** for `zeltechhub.store` to confirm global propagation matched the EC2 Public IPv4 address across international DNS resolvers.

---

### Phase 8 & 9: SSL/TLS Encryption (HTTPS) with Let's Encrypt
1. Stopped Nginx temporarily and installed Certbot along with the Nginx plugin:
   ```bash
   sudo systemctl stop nginx
   sudo apt install certbot python3-certbot-nginx -y
   ```
2. Requested and installed an SSL/TLS certificate for the domain:
   ```bash
   sudo certbot --nginx -d zeltechhub.store
   ```
3. Restarted Nginx to apply the HTTPS configuration:
   ```bash
   sudo systemctl start nginx
   ```

> **Operational Note:** Because standard EC2 public IPs change upon stopping and restarting an instance (unless an Elastic IP is attached), any instance restart requires updating the DNS **A Record** for `zeltechhub.store` with the new Public IPv4 address.

---

### Phase 10: Application Testing
* Verified HTTP-to-HTTPS automatic redirection (`http://zeltechhub.store` → `https://zeltechhub.store`).
* Confirmed valid SSL certificate status in the browser padlock menu.
* Tested responsive layouts, navigation links, and static asset loading across desktop and mobile viewports.

---

### Phase 11: External Penetration Testing & Vulnerability Scan
1. Conducted an external security and vulnerability scan against `zeltechhub.store` using [Pentest-Tools.com](https://pentest-tools.com).
2. Audited exposed ports, TLS/SSL cipher configurations, HTTP security headers, and web server signatures to ensure no critical vulnerabilities were exposed.

---

### Phase 12 & 13: Documentation & Live Showcase
* Documented the end-to-end architecture, CLI commands, and operational notes in this repository.
* Published the live deployment at [https://zeltechhub.shop](https://zeltechhub.store) for verification and portfolio review.

---

## 💡 Key Engineering Takeaways
* **Defense-in-Depth:** Combining AWS Security Groups (network-level firewall) with TLS encryption (transport-layer security) and VPC Flow Logs (visibility).
* **Full Lifecycle Ownership:** Managing every layer of cloud delivery—from IP CIDR subnetting and Linux server administration to DNS propagation and vulnerability scanning.
