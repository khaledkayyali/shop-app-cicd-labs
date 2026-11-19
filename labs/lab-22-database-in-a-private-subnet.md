# Lab 22: A database in a private subnet

**Session 8 · 35 minutes · EC2, RDS and VPC consoles, CloudShell**

**Goal:** put a managed MySQL database (Amazon RDS) in the private subnets of your VPC, put a web server in a public subnet, and prove that only the web server can reach the database. Then delete everything, including the VPC.

**Words you will need:** Amazon RDS · DB instance · endpoint · DB subnet group · Multi-AZ · read replica · automated backup

You need `<yourname>-vpc` from Lab 21. Region **eu-central-1**.

**Order matters today:** the database takes about 10 minutes to start, so you start it first (Steps 1 to 3) and launch the web server while you wait (Step 4).

## Step 1: Two security groups, inside your VPC

**EC2**, **Security Groups**, **Create security group**. In both, change **VPC** to `<yourname>-vpc` (the default VPC is chosen first, and that is the most common mistake today).

| Name | Inbound rules |
|---|---|
| `<yourname>-web-sg` | HTTP from **Anywhere-IPv4**. SSH from the prefix list `com.amazonaws.eu-central-1.ec2-instance-connect` (as in Lab 18) |
| `<yourname>-db-sg` | Type **MYSQL/Aurora** (port 3306), source: the security group `<yourname>-web-sg` |

Create `<yourname>-web-sg` first, so you can choose it as the source in the second one.

## Step 2: A DB subnet group

RDS must know which subnets it may use.

**RDS**, left menu **Subnet groups**, **Create DB subnet group**:

- Name and description: `<yourname>-db-subnets`
- VPC: `<yourname>-vpc`
- Availability Zones: `eu-central-1a` and `eu-central-1b`
- Subnets: only the two private ones, `10.0.11.0/24` and `10.0.12.0/24`

**Create.**

## Step 3: Start the database

**RDS**, **Databases**, **Create database**. Settings not in this table: leave them as they are.

| Setting | Value |
|---|---|
| Creation method | **Standard create** (in some consoles: **Full configuration**) |
| Engine | **MySQL**, the default version |
| Template | **Free tier** or **Sandbox** if you see one, otherwise **Dev/Test** |
| Availability | **Single-AZ DB instance** (1 instance) |
| DB instance identifier | `<yourname>-db` |
| Master username | `admin` |
| Credentials management | **Self managed**. Type a password and write it down |
| Instance class | Burstable: **db.t4g.micro** (or **db.t3.micro**) |
| Storage | gp3 (or gp2), **20** GiB, untick **Enable storage autoscaling** |
| Compute resource | **Don’t connect to an EC2 compute resource** |
| VPC | `<yourname>-vpc` |
| DB subnet group | `<yourname>-db-subnets` |
| Public access | **No** |
| VPC security group | **Choose existing**: remove `default`, add `<yourname>-db-sg` |
| Monitoring | untick **Enhanced Monitoring** if it is ticked |
| Additional configuration | Initial database name `shop`. Backup retention **1 day**. Deletion protection **off** |

**Create database.** The status is **Creating** for about 10 minutes. Go on with Step 4.

If AWS refuses with *not authorized*, check the instance class and that Multi-AZ is off: the course account allows only the smallest single-AZ databases.

## Step 4: A web server in a public subnet

**EC2**, **Launch instance**:

| Setting | Value |
|---|---|
| Name | `<yourname>-web` |
| AMI and type | Amazon Linux 2023, **t3.micro**, no key pair |
| Network settings, **Edit** | VPC `<yourname>-vpc`, subnet `<yourname>-public-a`, auto-assign public IP **Enable**, existing security group `<yourname>-web-sg` |
| User data | the script below |

```bash
#!/bin/bash
dnf install -y nginx mariadb105
TOKEN=$(curl -s -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 300")
ID=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/instance-id)
AZ=$(curl -s -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/availability-zone)
cat > /usr/share/nginx/html/index.html <<HTML
<h1>Shop App in my own VPC</h1>
<p>Served by $ID in $AZ</p>
HTML
systemctl enable --now nginx
```

When it runs, open `http://<public IP>`: the page must show. Your own VPC works.

## Step 5: Talk to the database

Wait until the database status is **Available**. Open it and copy the **Endpoint** from the **Connectivity & security** tab. It looks like `<yourname>-db.abc123xyz.eu-central-1.rds.amazonaws.com`.

Connect to `<yourname>-web` with **EC2 Instance Connect**, then:

```bash
EP=<paste your endpoint>
getent hosts $EP
mysql -h $EP -u admin -p shop
```

Type your password. At the `MySQL [shop]>` prompt:

```sql
CREATE TABLE products (id INT PRIMARY KEY AUTO_INCREMENT, name VARCHAR(50), price DECIMAL(6,2));
INSERT INTO products (name, price) VALUES ('Coffee', 12.50), ('Tea', 8.00), ('Cake', 15.00);
SELECT * FROM products;
SELECT @@version;
exit
```

**Question 53:** which IP address did `getent hosts` show for the endpoint, and in which of your subnets is it? Why do applications connect to the endpoint name and never to that IP?

## Step 6: Prove that the database is private

Open **CloudShell** (it runs outside your VPC, like any computer on the internet):

```bash
EP=<paste your endpoint>
getent hosts $EP
timeout 5 bash -c "</dev/tcp/$EP/3306" && echo "OPEN" || echo "BLOCKED"
```

**Question 54:** CloudShell prints `BLOCKED`. Give **three** separate reasons, from Lab 21 and from Steps 1 to 3, why the database cannot be reached from the internet.

## Step 7: What a managed database gives you

Open `<yourname>-db` and look, without changing anything:

1. Tab **Maintenance & backups**: the automated backup window, and the latest restorable time.
2. **Actions**: find **Restore to point in time** and **Create read replica**. Do not click them through.
3. **Modify**: find the **Multi-AZ** setting. Do not apply anything: click **Cancel**.

**Question 55:** what is the difference between **Multi-AZ** and a **read replica**? For each one, say what problem it solves.

**Question 56:** which parts of today's lab cost money every hour? What happens if you only **stop** the database instead of deleting it? *Hint: look up how long an RDS database can stay stopped.*

## Step 8: Clean up, in this order

1. **RDS**, select `<yourname>-db`, **Actions**, **Delete**. Untick **Create final snapshot** and **Retain automated backups**, tick the acknowledgement, type `delete me`. Deleting takes about 5 minutes.
2. **EC2**: terminate `<yourname>-web`.
3. When the database is gone: **RDS**, **Subnet groups**, delete `<yourname>-db-subnets`.
4. **EC2**, **Security Groups**: delete `<yourname>-db-sg` first (it points to the other one), then `<yourname>-web-sg`.
5. **VPC**, **Your VPCs**, select `<yourname>-vpc`, **Actions**, **Delete VPC**. AWS lists everything inside it (subnets, route tables, internet gateway, network ACL) and deletes it together. Type `delete`.
6. Check in CloudShell. Every command must print `[]`:

```bash
ME=omar-awaisha   # your name tag
aws rds describe-db-instances --query "DBInstances[?starts_with(DBInstanceIdentifier, '$ME')].DBInstanceIdentifier"
aws ec2 describe-vpcs --filters "Name=tag:Name,Values=$ME-*" --query 'Vpcs[].VpcId'
aws ec2 describe-instances --filters "Name=tag:Name,Values=$ME-*" "Name=instance-state-name,Values=pending,running,stopped" --query 'Reservations[].Instances[].InstanceId'
```

## Done when

You saw your three products come back from the database, CloudShell could not reach it, all three checks print `[]`, and you can answer Questions 53 to 56.

## Submit in Google Classroom

Your answers to Questions 49 to 56.

---

Previous: [Lab 21](lab-21-your-own-vpc.md) · Next: [Homework 8](homework-08.md) · [All labs](../README.md)
