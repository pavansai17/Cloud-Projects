# Cloud-Projects

A collection of hands-on AWS cloud projects — covering core networking, compute, and secure application deployment. Each project is documented with step-by-step commands and explanations so it can be reproduced or used as a learning reference.

## Repository Structure

| Folder / File | Description |
|---|---|
| [`VPC/`](./VPC) | Projects and notes related to setting up and configuring a custom AWS Virtual Private Cloud (subnets, route tables, gateways, security groups). |
| [`EC2/`](./EC2) | Projects related to launching, configuring, and managing AWS EC2 instances. |
| [`images/`](./images) | Screenshots and diagrams used as supporting evidence/visuals across the project docs. |
| [`Host-App-on-HTTPS.md`](./Host-App-on-HTTPS.md) | Step-by-step guide to hosting a web application on an Ubuntu EC2 instance and securing it with HTTPS using Apache2, DuckDNS, and Let's Encrypt (Certbot). |

## Featured Project: Hosting a Web App on AWS EC2 with HTTPS

**Stack:** Ubuntu · Apache2 · DuckDNS · Let's Encrypt

This project walks through:
1. Preparing an Ubuntu EC2 instance and installing Apache2
2. Placing application files in the Apache web root
3. Configuring EC2 security groups for HTTP (80) and HTTPS (443)
4. Setting up a free DuckDNS domain
5. Verifying Apache is serving traffic
6. Installing Certbot and requesting a Let's Encrypt TLS certificate
7. Confirming the app is served securely over HTTPS

Full walkthrough: [`Host-App-on-HTTPS.md`](./Host-App-on-HTTPS.md)

## Tech & Tools Used

- **AWS EC2** – Virtual compute instances
- **AWS VPC** – Custom networking setup
- **Ubuntu** – Server operating system
- **Apache2** – Web server
- **DuckDNS** – Free dynamic DNS provider
- **Certbot / Let's Encrypt** – Free SSL/TLS certificates

## Purpose

This repository serves as a personal knowledge base and portfolio of cloud computing projects, documenting real deployment steps for future reference and for anyone learning AWS fundamentals.



## Author

**[pavansai17](https://github.com/pavansai17)**
