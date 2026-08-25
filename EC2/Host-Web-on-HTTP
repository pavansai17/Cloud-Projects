# Host a Website Using EC2 in AWS

**Step-by-Step Guide to Deploy a Static Website on EC2**

---

## 1. Download Website Template

- Go to a website like Templatemo and choose any website template you like.
- Copy the download link address of that template.

**Example:**
```
https://tooplate.com/download/2165_neon_carbon.zip
```

---

## 2. Launch an EC2 Instance

- Launch an EC2 instance (Ubuntu recommended).
- Note down the Public IP address.

**Example Public IP:**
```
13.234.123.45
```

---

## 3. Edit Inbound Rules (Security Group)

- Go to EC2 Dashboard > Security Groups.
- Edit Inbound Rules and add:

| Type | Protocol | Port Range | Source |
|------|----------|------------|--------|
| HTTP | TCP | 80 | 0.0.0.0/0 |

This allows traffic on port 80 (HTTP).

---

## 4. Update System & Install Nginx

Update system:
```bash
sudo apt update
```

Install Nginx:
```bash
sudo apt install nginx -y
```

Check Nginx version:
```bash
nginx -v
```

**Expected Output (Example):**
```
nginx version: nginx/1.18.0 (Ubuntu)
```

---

## 5. Install Zip & Check Version

Install zip:
```bash
sudo apt install zip -y
```

Check zip version:
```bash
zip --version
```

---

## 6. Download Website Files (.zip)

- Download the template files to your EC2 instance using the copied link.
- Command:

```bash
wget -O app.zip https://templatemo.com/templates/templatemo_556_mini_finance.zip
```

---

## 7. Verify & Unzip Files

Check if file downloaded:
```bash
ls -lh app.zip
```

Unzip the file:
```bash
unzip app.zip
```

Check extracted files:
```bash
ls -l
```

---

## 8. Copy Files to Nginx Default Directory

Copy the extracted files to Nginx default directory:
```bash
sudo cp -r 2165_neon_carbon/* /var/www/html/
```

This is the default root directory served by Nginx:
```
/var/www/html/
├── index.html
├── css/
├── images/
├── js/
└── ...
```

---

## 9. Access Your Website

- Copy your EC2 Public IP address.
- Open a new tab in your web browser and enter:

```
http://<Public-IP>:80
```

**Example:**
```
http://13.234.123.45:80
```

---

## Ports to Remember

| Service | Port | Purpose |
|---------|------|---------|
| SSH | 22 | Secure Shell – Connect to the instance |
| HTTP | 80 | Web Traffic (Unsecured) |
| HTTPS | 443 | Web Traffic (Secure) |

---

## Summary

You downloaded a website template, launched an EC2 instance, installed Nginx, uploaded files, and accessed your website using your EC2 Public IP.

**Key Points:**
- Ensure port 80 is open in Security Group.
- Nginx serves files from `/var/www/html/` by default.
- Always keep your system updated.
