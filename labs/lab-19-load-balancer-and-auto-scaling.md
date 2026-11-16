# Lab 19: A load balancer and Auto Scaling

**Session 7 · 35 minutes · EC2 console**

**Goal:** run shop-app on two servers in two Availability Zones behind one address, and let AWS keep two servers running for you.

**Words you will need:** high availability · Application Load Balancer (ALB) · listener · target group · health check · launch template · Auto Scaling group (ASG) · desired capacity

You build six things, in this order. Each one needs the one before it:

| # | What | Name |
|---|---|---|
| 1 | Two security groups | `<yourname>-alb-sg`, `<yourname>-app-sg` |
| 2 | A launch template | `<yourname>-lt` |
| 3 | A target group | `<yourname>-tg` |
| 4 | A load balancer | `<yourname>-alb` |
| 5 | An Auto Scaling group | `<yourname>-asg` |
| 6 | A test in your browser | |

Region **eu-central-1**.

## Step 1: Two security groups

**EC2**, **Security Groups**, **Create security group**, in the default VPC:

1. `<yourname>-alb-sg`, description `Web traffic to the load balancer`. Inbound rule: **HTTP**, source **Anywhere-IPv4**. Create.
2. `<yourname>-app-sg`, description `Web traffic from my load balancer only`. Inbound rule: **HTTP**, source **Custom**, then choose the security group `<yourname>-alb-sg` from the list. Create.

The second rule does not name an address. It names a group: only things inside `<yourname>-alb-sg`, that is your load balancer, may reach the servers.

## Step 2: A launch template

A launch template is the recipe the Auto Scaling group uses every time it starts a server.

**Launch Templates**, **Create launch template**:

| Setting | Value |
|---|---|
| Name | `<yourname>-lt` |
| Auto Scaling guidance | tick **Provide guidance to help me set up a template that I can use with EC2 Auto Scaling** |
| AMI | Amazon Linux 2023 |
| Instance type | **t3.micro** (the course limit; anything bigger is against the rules) |
| Key pair | **Don't include in launch template** |
| Network settings, subnet | **Don't include in launch template** |
| Security groups | `<yourname>-app-sg` |
| Resource tags | Add tag `Name` = `<yourname>-app`, resource types **Instances** and **Volumes** |
| Advanced details, User data | the same script as Lab 17 |

Create the launch template.

## Step 3: A target group

**Target Groups**, **Create target group**:

- Target type **Instances**, name `<yourname>-tg`, protocol **HTTP**, port **80**, the default VPC.
- Health check path: `/`
- **Next**. Do **not** register any instance (the Auto Scaling group will). **Create target group**.

## Step 4: The load balancer

**Load Balancers**, **Create load balancer**, **Application Load Balancer**:

- Name `<yourname>-alb`, scheme **Internet-facing**, IP address type **IPv4**.
- Network mapping: the default VPC. Tick **eu-central-1a** and **eu-central-1b** (one subnet each).
- Security groups: remove `default`, choose `<yourname>-alb-sg`.
- Listener **HTTP : 80**, default action: forward to `<yourname>-tg`.
- **Create load balancer**. It takes a few minutes to become **Active**.

## Step 5: The Auto Scaling group

**Auto Scaling Groups**, **Create Auto Scaling group**:

1. Name `<yourname>-asg`, launch template `<yourname>-lt`, version **Latest**. **Next**.
2. Network: the default VPC, and the subnets in **eu-central-1a** and **eu-central-1b**. **Next**.
3. Load balancing: **Attach to an existing load balancer**, **Choose from your load balancer target groups**, `<yourname>-tg`. Health checks: tick **Turn on Elastic Load Balancing health checks**. **Next**.
4. Group size: **Desired 2**, **Min 2**, **Max 4**. Scaling: **No scaling policies**. **Next** until **Create Auto Scaling group**.

## Step 6: Test it

1. **Target Groups**, `<yourname>-tg`, tab **Targets**: wait until two targets are **healthy** (3 to 5 minutes).
2. **Load Balancers**, `<yourname>-alb`: copy the **DNS name**. Open `http://<DNS name>` in your browser.
3. Refresh the page about 10 times.

**Question 43:** what changes when you refresh, and what stays the same? Which part of AWS decides which server answers?

4. **Instances**: copy the public IP of one `<yourname>-app` server and open `http://<that IP>` directly.

**Question 44:** what happened when you opened the server's own address? Which rule caused it, and why is that safer than letting everyone reach the servers directly?

**Do not delete anything yet.** Lab 20 uses all of it.

---

Previous: [Lab 18](lab-18-inside-the-server.md) · Next: [Lab 20](lab-20-self-healing-and-cleanup.md) · [All labs](../README.md)
