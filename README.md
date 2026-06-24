# 🚀 Highly Available Web Application using Auto Scaling & Load Balancer

---

# 📖 Project Overview

This project demonstrates how to design and deploy a **Highly Available Web Application Architecture on AWS** that automatically scales based on demand and eliminates single points of failure.

The infrastructure uses:

- **Amazon EC2**
- **Custom AMI**
- **Launch Templates**
- **Auto Scaling Groups**
- **Application Load Balancer**
- **Multi-AZ Deployment**
- **CloudWatch Monitoring**

The architecture ensures the application remains **available during traffic spikes** such as flash sales or marketing campaigns.

---

# 📑 Table of Contents


Architecture Diagram  
Technologies Used  
Project Implementation  
Networking Setup  
EC2 Base Server  
Create AMI  
Launch Template  
Auto Scaling Group  
Application Load Balancer  
Failover Testing  
Scaling Validation  
High Availability Strategy  
Key Learning Outcomes  

---

# 🏗 Architecture Diagram

```
                        Users
                         │
                         ▼
                     Internet
                         │
                         ▼
                 Internet Gateway
                         │
                         ▼
        ┌─────────────────────────────────┐
        │               VPC               │
        │                                 │
        │     Application Load Balancer   │
        │              │                  │
        │              ▼                  │
        │     Target Group (ALB)          │
        │        │             │          │
        │        ▼             ▼          │
        │  Public Subnet AZ-A  Public Subnet AZ-B
        │        │             │
        │   EC2 Instance       EC2 Instance
        │  (Auto Scaling)     (Auto Scaling)
        │        │             │
        │        └──────┬──────┘
        │               ▼
        │        Auto Scaling Group
        │               │
        │               ▼
        │        CloudWatch Metrics
        │        (CPU Monitoring &
        │        Scaling Policies)
        │
        └─────────────────────────────────┘
```

---

# 🧰 Technologies Used

| AWS Service | Purpose |
|-------------|--------|
| **Amazon EC2** | Virtual servers hosting the application |
| **Amazon VPC** | Custom networking environment |
| **AMI** | Template for launching instances |
| **Launch Template** | Standardized instance configuration |
| **Auto Scaling Group** | Automatically scales EC2 instances |
| **Application Load Balancer** | Distributes incoming traffic |
| **CloudWatch** | Monitoring and scaling triggers |

---

# ⚙ Project Implementation

---

# 1️⃣ Networking Setup

A custom **Virtual Private Cloud (VPC)** was created.

### Configuration

- Custom **VPC**
- **2 Public Subnets**
- Subnets deployed in **different Availability Zones**
- **Internet Gateway** attached
- **Route Tables** configured

### 🎯 Result

This setup ensures:

- **High availability**
- **Fault tolerance**
- **Public access through Load Balancer**

---

# 2️⃣ Create EC2 Web Server (Base Instance)

A **base EC2 instance** was launched to configure the application.

### Instance Configuration

| Setting | Value |
|------|------|
| OS | Ubuntu Server |
| Instance Type | `t3.small` |
| Security Group | HTTP (80), SSH (22) |

---

# 📜 User Data Script For Both Server Just Change Version 2.0 in second Server

The following script installs **Nginx and deploys the web application automatically**.

```bash
#!/bin/bash

apt update -y
apt install nginx -y

systemctl start nginx
systemctl enable nginx

HOSTNAME=$(hostname)

cat <<EOF > /var/www/html/index.html
<!DOCTYPE html>
<html>
<head>
<title>My Web Application</title>

<style>
body {
font-family: Arial, sans-serif;
background-color: #f2f3f5;
display:flex;
justify-content:center;
align-items:center;
height:100vh;
}

.container{
background:white;
padding:40px;
border-radius:10px;
box-shadow:0px 2px 15px rgba(0,0,0,0.2);
text-align:center;
}
</style>

</head>

<body>

<div class="container">

<h1>Welcome to My Web Application</h1>
<h2>Version 1.0</h2>
<h3>Server Hostname: $HOSTNAME</h3>

<p>This page is deployed automatically using EC2 User Data.</p>

</div>

</body>
</html>
EOF

systemctl restart nginx
```

---

# 3️⃣ Create AMI (Amazon Machine Image)

After configuring the base server:

1. Select the EC2 instance
2. Click **Create Image**
3. Provide an **AMI Name**

### AMI Stores

- Operating system
- Nginx web server
- Application files

This AMI will be used to **launch identical instances automatically**.

---

# 4️⃣ Create Launch Template

A **Launch Template** standardizes server configuration.

### Configuration

- AMI → Custom Web Server AMI
- Instance Type → `t3.small`
- Security Group → HTTP access
- Key pair (optional)

### Benefit

Ensures **consistent instance deployment** across environments.

---

# 5️⃣ Create Auto Scaling Group (ASG)

The **Auto Scaling Group** automatically launches and replaces instances.

| Parameter | Value |
|------|------|
| Minimum Instances | 2 |
| Desired Capacity | 2 |
| Maximum Instances | 5 |
| Subnets | Multi-AZ Public Subnets |

---

### 📈 Scaling Policy

CPU-based scaling rules:

- **Scale Out:** CPU > 50%
- **Scale In:** CPU < 30%

This ensures infrastructure adapts to **traffic demand automatically**.

---

# 6️⃣ Configure Application Load Balancer

An **Application Load Balancer (ALB)** distributes traffic across EC2 instances.

### Configuration

- Internet-facing Load Balancer
- Listener → HTTP (Port 80)
- Target Group → Auto Scaling instances
- Health check path → `/`

### Benefits

- Traffic distribution
- Automatic removal of unhealthy instances
- Increased application availability

---

# 7️⃣ Failover Testing

To verify high availability:

1️⃣ Manually terminate an EC2 instance  
2️⃣ Auto Scaling launches a new instance  
3️⃣ Load Balancer routes traffic to healthy instances

✅ Result: **Application remains accessible without downtime**

---

# 8️⃣ Scaling Validation

Traffic load was generated to simulate peak demand.

### Observations

- CPU utilization increased
- Auto Scaling launched additional instances
- Load balancer distributed traffic evenly

Monitoring was done using **CloudWatch Metrics**.

---

# 📦 Deliverables

- Architecture Diagram
- EC2 configuration proof
- AMI creation proof
- Launch Template configuration
- Auto Scaling screenshots
- Load testing screenshots
- CloudWatch scaling logs
- Project documentation

---

# 🛡 High Availability Strategy

### Multi-AZ Deployment
Instances run across multiple availability zones.

### Auto Scaling
Automatically increases capacity during traffic spikes.

### Load Balancing
Distributes traffic across healthy instances.

### Health Checks
Unhealthy servers are automatically replaced.

---

# 🎓 Key Learning Outcomes

- Designing **Highly Available AWS Architectures**
- Creating **Custom AMIs**
- Implementing **Launch Templates**
- Deploying **Auto Scaling Groups**
- Configuring **Application Load Balancers**
- Monitoring infrastructure with **CloudWatch**

---

# 📸 Application Screenshots

## 🌐 Load Balancer Output

![LB Creation](images/LB-CREATION/LB-4.jpg)

---

## 🌐 Auto Scaling Output

![ASG Creation](images/ASG-CREATION/ASG-OUTPUT/ASG-OP-1.jpg)

---
## 🌐 Load Generate

![Stress Command](images/LOAD-GENERATE/STRESS-COMMAND.jpg)

---

## 🌐 Load Generate Activity

![Load Generate Activity](images/LOAD-GENERATE/asg-load-activity2.jpg)

---


## 🌐 Scaling Log

![Scaling Log](images/MONITOR/asg-cpuUtitlization-load-graph2.jpg)
