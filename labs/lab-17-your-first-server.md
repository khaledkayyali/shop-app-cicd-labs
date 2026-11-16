# Lab 17: Your first server

**Session 6 · 30 minutes · EC2 console**

**Goal:** start a Linux server in AWS that installs a web server by itself, open its page from your browser, see what changes when you stop and start it, and delete it.

**Words you will need:** EC2 · instance · AMI · instance type · security group · user data · public IP

## Step 1: Launch the instance

Open **EC2** (region **eu-central-1**), then **Launch instance**. Fill in:

| Setting | Value |
|---|---|
| Name | `<yourname>-web`, for example `sara-web` |
| Application and OS image | **Amazon Linux 2023** (the default) |
| Instance type | **t3.micro** |
| Key pair | **Proceed without a key pair** (you will connect another way in Session 7) |
| Network settings | Click **Edit**. Auto-assign public IP: **Enable** |
| Security group | **Create security group**, name `<yourname>-web-sg`. Remove the SSH rule. Add a rule: type **HTTP**, source **Anywhere** |
| Storage | Keep the default (8 GiB gp3) |

Open **Advanced details** and scroll to the end, to **User data**. Paste this script (copy exactly). The server runs it once, at its first start:

```bash
#!/bin/bash
dnf install -y nginx
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 300")
ID=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/instance-id)
AZ=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/availability-zone)
cat > /usr/share/nginx/html/index.html <<HTML
<h1>Shop App on AWS</h1>
<p>Served by $ID in $AZ</p>
HTML
systemctl enable --now nginx
```

Check the **Summary** on the right: 1 instance, t3.micro. Click **Launch instance**.

**Try it:** start the launch again with instance type **t3.large**. What happens? Cancel it.

## Step 2: Open your page

1. **Instances**, select `<yourname>-web`. Wait until **Status check** shows **2/2 checks passed** (about 2 minutes).
2. Copy the **Public IPv4 address**.
3. In your browser open `http://<that-address>`. Type `http://`, not `https://`: our server has no certificate.

**Question 37:** what instance ID and Availability Zone does your page show? Check them against the console. Which part of the launch made nginx install itself?

## Step 3: Stop and start

1. Write down the public IP.
2. **Instance state**, **Stop instance**. Wait for **Stopped**.
3. **Instance state**, **Start instance**. Wait for **Running** and **2/2 checks**.
4. Compare the public IP. Open the page with the new address.

**Question 38:** did the public IP change? Did the instance ID change? Why could this be a problem for a real website, and what is AWS's answer to it?

## Step 4: Look around

With your instance selected, open each tab: **Details**, **Security** (find your HTTP rule), **Networking** (find the subnet and the VPC), **Storage** (find the volume) and **Monitoring** (CPU).

**Question 39:** with this server, who is responsible for updating Linux and nginx: you or AWS? Who is responsible for the building, the power and the physical disks? What is this split called?

## Step 5: Clean up

1. **Instance state**, **Terminate (delete) instance**, confirm. Wait for **Terminated**.
2. **Security Groups** (left menu), select `<yourname>-web-sg`, **Actions**, **Delete security groups**.

A terminated instance stays in the list for about an hour, then disappears. It costs nothing.

## Done when

Your page worked, your instance is **Terminated**, your security group is deleted, and you can answer Questions 37 to 39.

## Submit in Google Classroom

Your answers to Questions 31 to 39.

---

Previous: [Lab 16](lab-16-s3-storage.md) · Next: [Homework 6](homework-06.md) · [All labs](../README.md)
