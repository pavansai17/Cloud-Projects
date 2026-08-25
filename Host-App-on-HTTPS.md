# Hosting a Web Application on AWS EC2 with HTTPS

**Ubuntu • Apache2 • DuckDNS • Let's Encrypt**

| | |
|---|---|
| **Objective** | Host a web application on an Ubuntu EC2 instance and secure it with HTTPS. |
| **Domain** | `pavansai17.duckdns.org` |
| **Web server** | Apache2 |
| **Certificate** | Let's Encrypt |
| **HTTPS port** | 443 |

This document presents the deployment flow in a concise, command-focused format. Each command is shown with its purpose for easy implementation and explanation.

---

## 1. Preparing the EC2 Instance

The application was hosted on an Ubuntu EC2 instance. Apache2 was installed and configured as the web server.

```bash
sudo apt update
```
**Meaning:** Refreshes the Ubuntu package index so the system can find current package information.

```bash
sudo apt install -y apache2
```
**Meaning:** Installs Apache2; `-y` automatically confirms the installation prompt.

```bash
sudo systemctl start apache2
```
**Meaning:** Starts the Apache2 service immediately.

```bash
sudo systemctl enable apache2
```
**Meaning:** Configures Apache2 to start automatically when the system boots.

```bash
sudo systemctl status apache2
```
**Meaning:** Displays the current Apache2 service status and confirms whether it is running.

```bash
sudo ss -tlnp | grep :80
```
**Meaning:** Checks listening TCP sockets and filters for port 80, confirming whether Apache is listening for HTTP traffic.

---

## 2. Web Application Location

The Apache web root is `/var/www/html`. Application files placed there can be served by Apache.

---

## 3. Security Group Configuration

The EC2 security group must permit the web traffic required by the deployment.

| Port | Protocol | Purpose |
|------|----------|---------|
| 80 | TCP | HTTP access and initial certificate validation |
| 443 | TCP | HTTPS access to the secured application |

---

## 4. DuckDNS Domain Configuration

DuckDNS provides the domain name used to access the EC2-hosted application. The configured hostname is `pavansai17.duckdns.org`.

```bash
dig +short pavansai17.duckdns.org
```
**Meaning:** Queries DNS and displays the IP address associated with the DuckDNS hostname.

---

## 5. Checking Apache and the Application

Before requesting the TLS certificate, the domain should be serving the application through Apache over HTTP.

```bash
sudo systemctl status apache2
```
**Meaning:** Confirms that the Apache2 web service is active.

```bash
sudo ss -tlnp | grep :80
```
**Meaning:** Confirms that the web server is listening on TCP port 80 for HTTP requests.

---

## 6. Installing Certbot (Ubuntu)

Certbot is used to request and configure the Let's Encrypt TLS certificate.

```bash
sudo apt install -y certbot python3-certbot-apache
```
**Meaning:** Installs Certbot and the Apache plugin, which allows Certbot to configure Apache for the certificate automatically.

---

## 7. Requesting and Installing the Certificate

The following command requests a certificate for the DuckDNS hostname and configures Apache to use HTTPS.

```bash
sudo certbot --apache -d pavansai17.duckdns.org
```
**Meaning:** Requests a Let's Encrypt certificate for the domain and uses the Apache plugin to install and configure it.

---

## 8. Final HTTPS Configuration

After the certificate is installed, Apache serves the application through HTTPS. The certificate is issued to `pavansai17.duckdns.org` by Let's Encrypt.

---

## 9. Final Result

| Component | Final state |
|-----------|-------------|
| EC2 / Ubuntu | Application host |
| Apache2 | Web server configured |
| DuckDNS | `pavansai17.duckdns.org` |
| Certbot | Installed for certificate management |
| Let's Encrypt | TLS certificate issued |
| HTTPS | Application available over port 443 |

---

## 10. Deployment Evidence — Final Screenshots

Final visual evidence of the HTTPS-secured application and secure browser connection.

> **Figure 1** — Browser security panel confirming a secure HTTPS connection for `pavansai17.duckdns.org`.
>
> *(Insert screenshot: `figure1-secure-connection.png`)*

> **Figure 2** — Hosted application successfully accessible through `pavansai17.duckdns.org` over HTTPS.
>
> *(Insert screenshot: `figure2-hosted-app.png`)*
