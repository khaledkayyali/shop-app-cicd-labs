# Lab 21: Your own network (VPC)

**Session 8 · 30 minutes · VPC console**

**Goal:** build a private network from zero: a VPC, two public and two private subnets in two Availability Zones, an internet gateway and two route tables. Then read the picture AWS draws of it.

**Words you will need:** VPC · CIDR · subnet · internet gateway · route table · public subnet · private subnet · network ACL

Region **eu-central-1**. Every name starts with your name tag, for example `omar-awaisha`. In this lab it is written `<yourname>`.

## The plan

| Name | What | CIDR | AZ |
|---|---|---|---|
| `<yourname>-vpc` | Your VPC | `10.0.0.0/16` | the whole region |
| `<yourname>-public-a` | Public subnet | `10.0.1.0/24` | `eu-central-1a` |
| `<yourname>-public-b` | Public subnet | `10.0.2.0/24` | `eu-central-1b` |
| `<yourname>-private-a` | Private subnet | `10.0.11.0/24` | `eu-central-1a` |
| `<yourname>-private-b` | Private subnet | `10.0.12.0/24` | `eu-central-1b` |

Every student uses the same numbers. That is fine: each VPC is its own separate network.

## Step 1: The VPC

1. Open **VPC**. Check the region is Frankfurt.
2. **Your VPCs**, **Create VPC**. Choose **VPC only** (not "VPC and more": today you build every piece by hand).
3. Name tag `<yourname>-vpc`, IPv4 CIDR `10.0.0.0/16`, no IPv6, tenancy **Default**. **Create VPC**.

**Question 49:** how many IP addresses are in `10.0.0.0/16`, and how many in one `/24` subnet? AWS keeps 5 addresses of every subnet for itself. How many can your servers use in one `/24`? *Hint: a /24 leaves 32 − 24 = 8 bits for addresses, and 2⁸ = 256.*

## Step 2: Four subnets

**Subnets**, **Create subnet**, VPC `<yourname>-vpc`. Add the four subnets from the plan in one go (**Add new subnet** for each): name, Availability Zone, and IPv4 subnet CIDR block exactly as in the table. **Create subnet**.

Then, for **each public subnet** only: select it, **Actions**, **Edit subnet settings**, tick **Enable auto-assign public IPv4 address**, **Save**.

## Step 3: A door to the internet

1. **Internet gateways**, **Create internet gateway**, name `<yourname>-igw`. Create.
2. On the green banner, **Attach to a VPC**, choose `<yourname>-vpc`, **Attach**.

## Step 4: Route tables

A subnet is public only if its route table sends internet traffic to the internet gateway.

1. **Route tables**, **Create route table**, name `<yourname>-public-rt`, VPC `<yourname>-vpc`. Create.
2. Tab **Routes**, **Edit routes**, **Add route**: destination `0.0.0.0/0`, target **Internet Gateway**, `<yourname>-igw`. **Save changes**.
3. Tab **Subnet associations**, **Edit subnet associations**, tick `<yourname>-public-a` and `<yourname>-public-b`. Save.
4. Create a second route table `<yourname>-private-rt` in the same VPC. Do **not** add any route. Associate `<yourname>-private-a` and `<yourname>-private-b`.

## Step 5: Read the picture

1. **Your VPCs**, select `<yourname>-vpc`, tab **Resource map**. AWS draws your subnets, route tables and the internet gateway, with lines between them.
2. Open `<yourname>-private-rt`, tab **Routes**. There is one route you did not add: `10.0.0.0/16` to `local`.
3. **Network ACLs**: find the one for your VPC (column **VPC**). Open its **Inbound rules**.

**Question 50:** what exactly makes `public-a` public and `private-a` private? Both are in the same VPC and the same AZ. What does the `local` route do?

**Question 51:** why did we make two private subnets in two different AZs, when one would be enough for one database? *Hint: think about Lab 22 and about what happens when one AZ fails.*

**Question 52:** the network ACL has rule `100` (allow all) and rule `*` (deny all). A security group never has a deny rule. Write two differences between a security group and a network ACL.

## Keep it

**Do not delete anything.** Lab 22 builds inside this VPC. If you stop here and continue another day, a VPC, subnets, route tables and an internet gateway cost nothing while they are empty.

## Done when

The resource map shows four subnets in two AZs, the public route table points `0.0.0.0/0` to your internet gateway, and you can answer Questions 49 to 52.

---

Previous: [Homework 7](homework-07.md) · Next: [Lab 22](lab-22-database-in-a-private-subnet.md) · [All labs](../README.md)
