# ☁️ CloudOps Lab

A hands-on Cloud and DevOps engineering lab focused on building, operating, troubleshooting, and automating cloud infrastructure.

This repository documents my practical learning journey across AWS, Linux administration, networking, web servers, containers, CI/CD, Infrastructure as Code, Kubernetes, and monitoring.

---

## 🚀 Day 1 — AWS EC2, Linux & Nginx

### Objective

Provision and configure a Linux-based web server on AWS, establish secure remote access, perform basic Linux administration, troubleshoot network connectivity, and deploy a custom static website using Nginx.

---

## 🏗️ Architecture

```text
                    Internet
                       │
                       │ HTTP :80
                       ▼
               ┌─────────────────┐
               │ Security Group  │
               │                 │
               │ SSH  :22        │
               │ HTTP :80        │
               └────────┬────────┘
                        │
                        ▼
               ┌─────────────────┐
               │    AWS EC2      │
               │  Ubuntu Linux   │
               │                 │
               │     Nginx       │
               │       │         │
               │       ▼         │
               │ /var/www/html   │
               │   index.html    │
               └─────────────────┘
```

---

## 🛠️ Technologies Used

- AWS EC2
- AWS Security Groups
- Ubuntu Linux
- SSH
- Nginx
- HTTP
- Linux CLI
- GitHub Issues
- GitHub Projects

---

## ☁️ AWS EC2 Provisioning

A development EC2 instance was provisioned in the AWS Frankfurt region (`eu-central-1`) using Ubuntu Linux.

The instance was configured as a small development server for practicing Linux administration and CloudOps operations.

During provisioning, I worked with:

- EC2 instances
- AMIs
- Instance types
- EBS storage
- Public and private IPv4 addresses
- Security Groups
- SSH key-based authentication
- Instance start/stop lifecycle

---

## 🔐 SSH Access

Remote administration of the server was performed using SSH with public-key authentication.

Example:

```bash
ssh -i cloudops-dev-key.pem ubuntu@<PUBLIC_IP>
```

SSH access was restricted using the EC2 Security Group rather than unnecessarily exposing port `22` to the entire internet.

Because the instance does not currently use an Elastic IP, its public IPv4 address may change after a stop/start cycle.

---

## 🐧 Linux Server Administration

After connecting to the instance, basic system and resource checks were performed.

### User and system identity

```bash
whoami
hostname
pwd
```

### Network interfaces

```bash
ip addr
```

### Memory usage

```bash
free -h
```

### Filesystem usage

```bash
df -h
```

### Directory disk usage

```bash
sudo du -sh /var/*
```

### Running processes

```bash
ps aux
ps aux | grep ssh
```

These commands were used to inspect the operating system, network configuration, memory, storage, directories, and running processes.

---

## 🌐 Nginx Web Server

Nginx was installed as the HTTP web server.

```bash
sudo apt update
sudo apt install nginx
```

The service status was verified with:

```bash
systemctl status nginx
```

Running Nginx processes were inspected with:

```bash
ps aux | grep nginx
```

Port `80` was verified as listening with:

```bash
ss -tulpn | grep :80
```

The web server was then tested locally:

```bash
curl http://localhost
```

This confirmed that Nginx was running and responding to HTTP requests from inside the EC2 instance.

---

## 🔎 HTTP Connectivity Troubleshooting

During the lab, Nginx worked correctly through `localhost`, but the website was initially not reachable from an external browser.

The investigation included checking:

1. Nginx service status
2. Nginx processes
3. Listening ports
4. Local HTTP connectivity
5. AWS Security Group inbound rules

Since:

```bash
curl http://localhost
```

returned the Nginx page and port `80` was listening, the application itself was working correctly.

The issue was identified at the AWS network access layer: the Security Group initially allowed SSH traffic but did not allow inbound HTTP traffic.

An inbound HTTP rule for TCP port `80` was added.

After updating the Security Group, the Nginx server became reachable through the EC2 public IPv4 address.

### Root Cause

Missing inbound HTTP (`TCP/80`) rule in the EC2 Security Group.

### Resolution

Allow inbound HTTP traffic on port `80` through the required Security Group rule.

---

## 💻 Custom Website Deployment

Instead of keeping the default Nginx welcome page, a custom CloudOps Lab page was deployed.

The default Nginx web directory was inspected:

```bash
cd /var/www/html
ls -la
```

The original Nginx page was backed up before making changes:

```bash
sudo cp index.nginx-debian.html index.nginx-debian.html.backup
```

A custom page was then created:

```bash
sudo nano index.html
```

The deployed page contains information about the lab environment and verifies that the EC2 + Nginx deployment is operational.

The final deployment path is:

```text
/var/www/html/index.html
```

Local verification:

```bash
curl http://localhost
```

External verification was performed by accessing the EC2 public IPv4 address from a browser.

---

## 📸 Deployment Result

The custom CloudOps Lab page is successfully served by Nginx from the AWS EC2 instance.

![CloudOps Lab running on AWS EC2](cloudops-lab-web.png)

---

## 📁 File Transfer with SCP

The deployed website source was transferred securely from the remote EC2 instance to the local Windows machine using SCP.

Example:

```bash
scp -i cloudops-dev-key.pem ubuntu@<PUBLIC_IP>:/var/www/html/index.html <LOCAL_PATH>
```

This allowed the deployed source code to be stored and versioned in this GitHub repository.

> Private key files such as `.pem` files must never be committed to the repository.

---

## 🧠 Key Learnings

This lab provided hands-on practice with:

- Provisioning an AWS EC2 instance
- Connecting securely to Linux servers using SSH
- Understanding public and private IP addresses
- Working with AWS Security Groups
- Inspecting Linux system resources
- Managing and inspecting Linux processes
- Installing and operating Nginx
- Understanding listening ports
- Testing HTTP services using `curl`
- Troubleshooting connectivity across application and network layers
- Understanding the Nginx web root
- Working with Linux file permissions
- Deploying custom static web content
- Transferring files between remote and local systems using SCP
- Documenting engineering work with GitHub Issues and Projects

---

## 🧩 Troubleshooting Approach

One of the main lessons from this lab was to troubleshoot connectivity layer by layer rather than changing configurations randomly.

```text
Browser
   ↓
Public IP
   ↓
AWS Security Group
   ↓
TCP Port 80
   ↓
Nginx
   ↓
/var/www/html/index.html
```

Testing each layer individually makes it easier to isolate the root cause of infrastructure problems.

---

## 📌 Project Progress

### Day 1 — Completed ✅

- [x] Provision AWS EC2 instance
- [x] Configure secure SSH access
- [x] Inspect Linux system resources
- [x] Inspect networking and processes
- [x] Install Nginx
- [x] Verify Nginx service
- [x] Verify listening HTTP port
- [x] Test HTTP locally with `curl`
- [x] Configure HTTP access through Security Group
- [x] Troubleshoot external connectivity
- [x] Deploy custom static website
- [x] Transfer deployed source using SCP
- [x] Document work using GitHub Issues and Projects

### Next

**Day 2 — AWS Networking & IAM**

---

# 🌐 Day 2 — AWS Networking & IAM

## Objective

Understand and validate the networking architecture used by the AWS EC2 development server and learn the fundamentals of AWS Identity and Access Management (IAM).

---

## 🌍 VPC Architecture

The EC2 instance is currently deployed inside the AWS default VPC in the Frankfurt region (`eu-central-1`).

```text
Internet
    │
    ▼
Internet Gateway
    │
    ▼
┌───────────────────────────────────────┐
│ VPC: 172.31.0.0/16                   │
│                                       │
│ Route Table                           │
│ ├── 172.31.0.0/16 → local            │
│ └── 0.0.0.0/0 → Internet Gateway     │
│                                       │
│ ┌───────────────────────────────────┐ │
│ │ Public Subnet                    │ │
│ │ 172.31.32.0/20                   │ │
│ │ Availability Zone: eu-central-1b │ │
│ │                                   │ │
│ │ Network ACL                       │ │
│ │        ↓                          │ │
│ │ Security Group                    │ │
│ │        ↓                          │ │
│ │ EC2 Instance                      │ │
│ │ Private IP: 172.31.35.155         │ │
│ └───────────────────────────────────┘ │
│                                       │
└───────────────────────────────────────┘
```

The Internet Gateway is attached to the VPC and provides a path between the VPC and the internet.

---

## 🧩 VPC and CIDR

The VPC uses the following IPv4 CIDR block:

```text
172.31.0.0/16
```

The `/16` prefix defines the network portion of the address range.

The EC2 instance is deployed in the subnet:

```text
172.31.32.0/20
```

This subnet is a smaller address range within the VPC.

The EC2 private IPv4 address:

```text
172.31.35.155
```

belongs to both the VPC address range and the subnet address range.

---

## 🏢 Availability Zones and Subnets

The default VPC contains multiple subnets distributed across Availability Zones.

The Network ACL inspected during the lab was associated with three subnets:

```text
eu-central-1a → 172.31.16.0/20
eu-central-1b → 172.31.32.0/20
eu-central-1c → 172.31.0.0/20
```

The development EC2 instance is located in:

```text
eu-central-1b
└── 172.31.32.0/20
    └── EC2: 172.31.35.155
```

A VPC is regional, while each subnet belongs to a single Availability Zone.

---

## 🛣️ Route Table

The subnet's route table contained two important routes:

```text
Destination       Target

172.31.0.0/16  →  local
0.0.0.0/0      →  Internet Gateway
```

### Local Route

```text
172.31.0.0/16 → local
```

Traffic destined for resources inside the VPC can remain within the VPC network.

### Default Internet Route

```text
0.0.0.0/0 → Internet Gateway
```

IPv4 traffic that does not match a more specific route can be directed toward the Internet Gateway.

The presence of a default route to an Internet Gateway is a key characteristic of a public subnet.

---

## 🌐 Internet Gateway

An Internet Gateway (IGW) is attached to the VPC and provides connectivity between the VPC and the internet when the required routing and addressing configuration is present.

The relationship inspected during the lab was:

```text
Internet
    ↕
Internet Gateway
    ↕
VPC
    │
    └── Public Subnet
            │
            └── EC2
```

The Internet Gateway alone does not make an EC2 instance publicly reachable.

Public connectivity also depends on components such as:

- Public IPv4 addressing
- Route table configuration
- Security Group rules
- Network ACL rules

---

## 🔐 Security Groups

The EC2 instance uses the Security Group:

```text
cloudops-dev-sg
```

Security Groups operate at the network interface (ENI) level.

### Inbound Rules

The development server was configured to allow:

```text
SSH  TCP/22 → Administrator public IP
HTTP TCP/80 → 0.0.0.0/0
```

SSH access is restricted while the web server is publicly reachable over HTTP.

### Outbound Rules

The Security Group currently allows outbound IPv4 traffic:

```text
All traffic → 0.0.0.0/0
```

This allows the instance to initiate connections to external services when required.

### Stateful Behavior

Security Groups are stateful.

When traffic is allowed in one direction, response traffic belonging to that established connection is automatically allowed.

---

## 🛡️ Network ACL

A Network Access Control List (NACL) provides traffic filtering at the subnet level.

The default NACL associated with the development subnet currently allows IPv4 traffic in both directions.

### Inbound

```text
Rule 100 → 0.0.0.0/0 → ALLOW
Rule *   → Remaining traffic → DENY
```

### Outbound

```text
Rule 100 → 0.0.0.0/0 → ALLOW
Rule *   → Remaining traffic → DENY
```

NACL rules are evaluated starting with the lowest rule number, and processing stops when a matching rule is found.

Unlike Security Groups, NACLs are stateless. Inbound and outbound traffic must therefore be considered separately.

---

## 🔎 Security Group vs Network ACL

| Feature | Security Group | Network ACL |
|---|---|---|
| Scope | ENI / EC2 | Subnet |
| Stateful | Yes | No |
| Allow rules | Yes | Yes |
| Deny rules | No | Yes |
| Rule processing | All applicable rules | Lowest-numbered matching rule first |
| Return traffic | Automatically tracked | Must be handled by rules |

---

## 🌍 Public vs Private Subnets

### Public Subnet

A public subnet has a route to an Internet Gateway.

Example:

```text
0.0.0.0/0 → Internet Gateway
```

The subnet used by the development EC2 instance is public because its route table contains this route.

For an EC2 instance to communicate directly with the internet over IPv4, additional requirements such as appropriate public addressing and security rules must also be satisfied.

### Private Subnet

A private subnet does not have a direct route to an Internet Gateway for internet-bound traffic.

Private subnets are commonly used for resources that should not be directly exposed to the internet, such as:

- Application servers
- Internal services
- Databases

---

## 🔄 NAT Gateway

Resources inside private subnets may still need outbound internet access for operations such as downloading packages or updates.

A common architecture uses a NAT Gateway:

```text
Private EC2
     │
     ▼
Private Subnet Route Table
     │
     ▼
NAT Gateway
(in a Public Subnet)
     │
     ▼
Internet Gateway
     │
     ▼
Internet
```

This allows private resources to initiate outbound IPv4 connections without making them directly reachable through the NAT Gateway from the public internet.

A NAT Gateway was not created during this lab to avoid unnecessary AWS charges.

---

# 🔑 AWS IAM Fundamentals

## IAM Overview

AWS Identity and Access Management (IAM) controls authentication and authorization for AWS resources.

The four fundamental concepts reviewed were:

```text
User
Group
Role
Policy
```

### IAM User

Represents an identity with credentials, commonly used for a person or workload where an IAM user is appropriate.

The lab uses an IAM user named:

```text
cloudops-admin
```

### IAM Group

A collection of IAM users.

Groups make it possible to assign permissions to multiple users through shared policies.

### IAM Role

An IAM Role is an assumable identity that can provide temporary permissions to trusted users, applications, or AWS services.

Example:

```text
EC2
 ↓
IAM Role
 ↓
Policy
 ↓
S3
```

Instead of storing long-lived AWS access keys on an EC2 instance, a role can provide temporary credentials to the workload.

### IAM Policy

A policy defines permissions.

In simple terms:

```text
Role/User → WHO receives permissions?
Policy    → WHAT is allowed or denied?
```

---

## 📜 IAM Policy Structure

Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "ec2:DescribeInstances",
      "Resource": "*"
    }
  ]
}
```

Important fields:

- `Effect` → Allow or Deny
- `Action` → AWS API operation
- `Resource` → Resource(s) the statement applies to
- `Statement` → One or more permission statements

---

## 🚫 IAM Access Denied Investigation

While exploring IAM, the `cloudops-admin` user received Access Denied responses for operations including:

```text
iam:ListUsers
iam:ListPolicies
```

The error indicated that no identity-based policy attached to the current identity allowed those actions.

This demonstrated an important IAM principle:

> A user being able to manage EC2 or VPC resources does not automatically mean that the user can administer IAM.

The name of an IAM user also does not determine its permissions. Effective permissions are controlled by policies and other applicable authorization controls.

---

## 🔒 Principle of Least Privilege

IAM permissions should follow the Principle of Least Privilege:

> Grant only the permissions required to perform the intended task.

This reduces unnecessary access and limits the potential impact of credential misuse or configuration mistakes.

---

## 🧠 Day 2 Key Learnings

- VPC and subnet relationships
- IPv4 CIDR notation
- Availability Zones
- Public and private IPv4 addressing
- Route tables
- Local routes
- Default routes
- Internet Gateways
- Public and private subnet concepts
- Security Group inbound and outbound rules
- Stateful firewall behavior
- Network ACLs
- Stateless firewall behavior
- Security Group vs NACL
- NAT Gateway architecture
- IAM Users
- IAM Groups
- IAM Roles
- IAM Policies
- IAM Policy JSON structure
- Access Denied troubleshooting
- Principle of Least Privilege

---

## ✅ Day 2 Status

- [x] VPC inspected
- [x] VPC CIDR identified
- [x] Subnet inspected
- [x] Subnet CIDR identified
- [x] Availability Zone reviewed
- [x] Route table inspected
- [x] Internet Gateway identified
- [x] Public/private IP concepts reviewed
- [x] Security Group rules inspected
- [x] Network ACL inspected
- [x] Security Group vs NACL understood
- [x] Public vs private subnet concepts reviewed
- [x] NAT Gateway architecture reviewed
- [x] IAM fundamentals reviewed
- [x] IAM Policy structure reviewed
- [x] Least Privilege principle reviewed
- [x] Networking and IAM concepts documented

### Next

**Day 3 — Custom AWS Network Architecture & Service Integration**

## 🔭 Roadmap

Future labs will expand this project with:

- AWS VPC and networking
- IAM users, roles, and policies
- S3 and AWS service integration
- Docker
- Docker Compose
- CI/CD pipelines
- Terraform
- Kubernetes
- Monitoring and observability
- Prometheus and Grafana
- Infrastructure automation
- Cloud security practices

---

## 👩‍💻 Author

**Merve Cura**

Computer Engineer focused on Cloud & DevOps Engineering.
