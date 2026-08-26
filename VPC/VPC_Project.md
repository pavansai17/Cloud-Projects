# VPC Project
## Implementation of Multi-AZ Scalable Infrastructure using AWS VPC, ALB, and NAT Gateway

---

## VPC Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                    VPC                                      │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │                              IGW                                    │   │
│  │                               ▲                                     │   │
│  │                               │                                     │   │
│  │  ┌─────────────────┐    ┌────┴────┐    ┌─────────────────┐         │   │
│  │  │  PUBLIC         │    │         │    │  PUBLIC         │         │   │
│  │  │  SUBNET-01      │◄───┤  Load   ├───►│  SUBNET-02      │         │   │
│  │  │  (ap-northeast-3a)│   │Balancer │    │  (ap-northeast-3b)│        │   │
│  │  │  ┌───────────┐  │    │         │    │                 │         │   │
│  │  │  │  NAT      │  │    └────┬────┘    │                 │         │   │
│  │  │  │  gateway  │  │         │         │                 │         │   │
│  │  │  └───────────┘  │         ▼         │                 │         │   │
│  │  │                 │    ┌─────────┐      │                 │         │   │
│  │  └─────────────────┘    │ Target  │      └─────────────────┘         │   │
│  │                         │  Group  │                                  │   │
│  │                         └────┬────┘                                  │   │
│  │                              │                                      │   │
│  │  ┌───────────────────────────┼─────────────────────────────────┐    │   │
│  │  │  PRIVATE                  │                                 │    │   │
│  │  │  SUBNET-01                │         PRIVATE                 │    │   │
│  │  │  ┌──────┐  ┌──────┐      │         SUBNET-02               │    │   │
│  │  │  │ins-01│  │ins-02│      │         ┌──────┐  ┌──────┐      │    │   │
│  │  │  └──────┘  └──────┘      │         │ins-03│  │ins-04│      │    │   │
│  │  │  (ap-northeast-3a)        │         └──────┘  └──────┘      │    │   │
│  │  │                           │         (ap-northeast-3b)       │    │   │
│  │  └───────────────────────────┴─────────────────────────────────┘    │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Route Tables:**
- **Public Route Table:** `0.0.0.0/0` → Internet Gateway
- **Private Route Table:** `0.0.0.0/0` → NAT Gateway

---

## Components Used in Project

1. Private and public subnets
2. Route tables
3. Internet Gateway
4. NAT gateway
5. EC2 instances
6. Auto Scaling Group
7. Elastic Load Balancer

---

## Steps to Implement

### 1. Create a VPC Network and Setup Subnets

#### Create VPC
- Go to **VPC service** in AWS and click on **Create VPC**
- Provide the name for VPC and set CIDR block (e.g., `10.0.0.0/16`)
- Click on **Next**

#### Create Subnets
- Go to **Subnets** in VPC dashboard and click on **Create subnets**
- Provide the subnet name and select one availability zone for each subnet
- Set the subnet CIDR block (e.g., `10.0.0.0/22`)
- Click on **Add subnet** for adding more subnets, then click on **Create subnets**

#### Subnet Configuration (4 Subnets with /22 CIDR block)

| Subnet Name        | Subnet CIDR    | Availability Zone  |
|:-------------------|:---------------|:-------------------|
| public-subnet-01   | 10.0.0.0/22    | ap-northeast-3a    |
| public-subnet-02   | 10.0.4.0/22    | ap-northeast-3b    |
| private-subnet-01  | 10.0.8.0/22    | ap-northeast-3a    |
| private-subnet-02  | 10.0.12.0/22   | ap-northeast-3b    |

#### Create Route Tables
- Once subnets are created, go to **Route Tables** and click on **Create route table**
- Provide route table name and click on **Create route table**
- **Create 2 route tables:** one as **Public RT** and one as **Private RT**

#### Attach Subnets to Route Tables
- Select the **Public RT** → click on **Actions** → **Edit subnet associations** → select all **public subnets**
- Select the **Private RT** → click on **Actions** → **Edit subnet associations** → select all **private subnets**

---

### 2. Create and Attach Internet Gateway

#### Create Internet Gateway
- In VPC dashboard, click on **Internet gateway** and click on **Create Internet gateway**
- Select the VPC in which to create the Internet gateway
- Click on **Create internet gateway**

#### Attach Internet Gateway to VPC
- Once created, click on **Actions** → **Attach to VPC**

#### Attach Public Route Table to Internet Gateway
- Go to **Route Tables** → select **Public route table**
- Click on **Actions** → **Edit routes**
- Add source: `0.0.0.0/0` and destination: **Internet gateway** → select the created internet gateway → **Save changes**

---

### 3. Create NAT Gateway

- In VPC dashboard, click on **NAT gateway** → **Create NAT gateway**
- Give the name for NAT gateway, select **Zonal**, and select a **public subnet**
- Allocate one **Elastic IP** and click on **Create NAT gateway**

#### Attach Private Route Table to NAT Gateway
- Once NAT gateway is created, go to **Private Route table**
- Click on **Actions** → **Edit routes**
- Add source: `0.0.0.0/0` and destination: **NAT gateway** → select the created NAT gateway

> **Resource Map:** Once all components are created and attached, the VPC resource map shows VPC, 4 Subnets, 3 Route Tables, and 1 Network Connection (IGW).

---

### 4. Create Auto Scaling Group and Load Balancers

#### Launch EC2 Instance and Host Application in Default VPC
- Create one EC2 instance by selecting **Ubuntu** as OS
- Select VPC as **default VPC** and allow **HTTP (80)** and **SSH (22)** port in security group
- Click on **Launch instance**
- Once launched, connect to it and host an application

#### Host Application Inside EC2

```bash
sudo apt update
sudo apt install unzip
sudo apt install nginx
wget -O app.zip <download-link-of-template>
unzip app.zip
```

- Copy the extracted folder to nginx path:

```bash
sudo cp -r <extracted-folder-name>/* /var/www/html
```

- Expose the application by copying the public IP of instance and accessing it via browser:

```
http://<public-IP-instance>:80
```

---

### 5. Create AMI and Launch Template

#### Create an Image via AMI
- Once instance is launched successfully, go to **Instance dashboard**
- Click on the instance → **Actions** → **Images and templates** → **Create image**
- Provide the image name and click on **Create image**
- Go to **AMI** and verify the image is in **available** state

#### Create a Launch Template
- Go to **Launch Template** in instance dashboard → click **Create Launch template**
- Give the template name and tick the option which provides auto scaling guidance
- Select the image which has been created under **My AMI**
- Choose instance type: **t2.micro**
- Create one key pair
- In network settings under security group, select default and select security group for created VPC by referring the VPC ID
- Click on **Launch template**

---

### 6. Create Auto Scaling Group and Attach Load Balancer

- In instance dashboard, click on **Auto scaling group** → **Create auto scaling group**
- Provide the name for auto scaling group
- Choose the created template and click on **Next**
- In **Network and availability zones**, select **2 private subnets** (created in different availability zones) and select **Balanced best effort**
- Click on **Next**
- In **Load Balancer** option, click on **Attach to a new Load Balancer**
- Give the load balancer name and select load balancer type as **Application Load Balancer**
- Under load balancer scheme, select **Internet facing**
- Select subnets as **public** in availability zones
- Under **Target and listener**, click on **Create a new target group** and give target group name
- Click on **Next**
- Select:
  - **Desired instance:** 1
  - **Minimum instance:** 1
  - **Maximum instance:** 3
- Under **Scaling policy**, select **Target tracking scaling policy**
- Select metric as **CPU utilization** and target value as **5**
- Click on **Next**, skip notification and tags, then click on **Next**
- Make sure:
  - Load balancer is in **active** state
  - Auto scaling group is created
  - Desired instance is present in instance dashboard
  - Application is accessible from load balancer DNS endpoint

> **Note:** If not accessible, go to security group in load balancer and add **HTTP (80)** and **SSH (22)** port.

**Application accessed via Load Balancer:**
- URL format: `http://<elb-dns-endpoint>`

---

### 7. SSH Bastion Host and Scale Testing

#### Launch EC2 Instance in Public Subnet (Bastion Host)
- Go to **EC2 dashboard** → click on **Launch instance**
- Select OS as **Ubuntu** and instance type as **t2.micro**
- Attach the created key pair
- In network settings, click on **Edit** and select the created VPC
- Choose any **public subnet** and **enable public IP**
- Choose security group as **default** (present in created VPC)
- Click on **Launch instance**

#### Connect to Bastion and SSH into Private Instance
- Connect to the created EC2 instance which has public IP address
- Create one file as `key.pem` and add private keypair content
- Give read permission:

```bash
chmod 400 key.pem
```

- SSH into private instance:

```bash
ssh -i "key.pem" ubuntu@<private-IP-desired-instance>
```

- Make sure you have logged into the private instance (desired instance)

#### Increase Load for Auto Scaling Test
- Once connected to the desired instance via SSH, install stress:

```bash
sudo apt update && sudo apt install stress -y
```

- Run stress command to increase CPU load:

```bash
stress --cpu 2 --timeout 300
```

- Verify for a few minutes and observe whether instances are scaled up in the EC2 dashboard

---

## Verification Checklist

| Component            | Status Check                                      |
|:---------------------|:--------------------------------------------------|
| VPC                  | Created with CIDR `10.0.0.0/16`                   |
| Subnets              | 4 subnets across 2 AZs                          |
| Route Tables         | Public RT + Private RT with correct associations  |
| Internet Gateway     | Attached to VPC and Public RT                     |
| NAT Gateway          | Created in public subnet, linked to Private RT  |
| EC2 (Bastion)        | Running in public subnet with public IP         |
| EC2 (App)            | Running in private subnets                        |
| AMI                  | Created from configured instance                  |
| Launch Template      | Created with custom AMI                           |
| Auto Scaling Group   | Desired: 1, Min: 1, Max: 3                        |
| Application LB       | Internet-facing, attached to public subnets       |
| Target Group         | Healthy targets registered                        |
| Scaling Policy       | Target tracking on CPU at 5%                      |
| Application Access   | Accessible via ALB DNS endpoint                   |
| Scale Up Test        | Verified via `stress` command                     |

---

## Architecture Summary

This project implements a **Multi-AZ scalable infrastructure** on AWS with the following characteristics:

- **High Availability:** Resources distributed across `ap-northeast-3a` and `ap-northeast-3b`
- **Security:** Private instances have no direct internet access; outbound traffic routed via NAT Gateway
- **Scalability:** Auto Scaling Group scales based on CPU utilization (target: 5%)
- **Accessibility:** Application Load Balancer serves as the single entry point for user traffic
- **Bastion Host:** Public EC2 instance enables secure SSH access to private instances

---

*Generated from VPC Project documentation.*
