# Homework 8: The network as code

**After Session 8 · before Session 9 · about 3 hours · 100 points**

In class you built a VPC by clicking, and deleted it at the end. Here you build the same VPC with commands, so that one command builds it and one command removes it. You also plan a bigger network on paper, and answer five design questions like the ones in the AWS Solutions Architect exam.

**Before you start:** sign in, choose **eu-central-1**, open CloudShell. Every name starts with your name tag. Delete everything at the end. A VPC with nothing running inside it costs nothing, but your account may hold only a few VPCs at a time, so never leave one behind.

## Part A: Labs 21 and 22 (15 points)

Labs 21 and 22 are done and Questions 49 to 56 are answered. Paste the output of the three check commands from Lab 22, Step 8. Each must print `[]`.

## Part B: The Lab 21 VPC with the AWS CLI (35 points)

Write two scripts and run them in CloudShell.

**`vpc.sh`** builds exactly the network from Lab 21, with the same names and CIDRs:

1. The VPC `<yourname>-vpc`, `10.0.0.0/16`. Save its ID in a variable.
2. The four subnets, each with its `Name` tag and Availability Zone.
3. The internet gateway `<yourname>-igw`, attached to the VPC.
4. The route table `<yourname>-public-rt` with the route `0.0.0.0/0` to the internet gateway, associated with both public subnets.
5. Auto-assign public IPv4 turned on for both public subnets.
6. The route table `<yourname>-private-rt`, associated with both private subnets, with no extra route.
7. At the end, print a table of the subnets: name, CIDR, AZ, and whether they get a public IP.

**`vpc-delete.sh`** removes everything `vpc.sh` made, in an order AWS accepts, and then prints the result of `aws ec2 describe-vpcs --filters "Name=tag:Name,Values=$ME-*" --query 'Vpcs[].VpcId'`, which must be `[]`.

*Hints:* `aws ec2 create-vpc --cidr-block ... --tag-specifications "ResourceType=vpc,Tags=[{Key=Name,Value=$ME-vpc}]" --query Vpc.VpcId --output text`. Then `create-subnet` (with `--availability-zone`), `create-internet-gateway`, `attach-internet-gateway`, `create-route-table`, `create-route`, `associate-route-table`, `modify-subnet-attribute --map-public-ip-on-launch`. To delete, find each ID again with `describe-...` and a filter on your `Name` tags or on `vpc-id`. An internet gateway must be **detached** before it can be deleted, and a route table must be **disassociated**, or simply deleted after its subnets are gone. The VPC goes last.

Run `vpc.sh`, open the **Resource map** of your VPC, and take a screenshot. Then run `vpc-delete.sh`.

Submit both scripts, the screenshot, and the output of `vpc-delete.sh`.

## Part C: Plan a bigger network (20 points)

Shop-app grows. The team asks you to plan a VPC with the CIDR `10.20.0.0/16` in **three** Availability Zones (`eu-central-1a`, `1b`, `1c`) and **three** tiers:

- **public**: only the load balancer lives here
- **app**: the servers, no public IP
- **data**: the databases, no public IP and no route to the internet

1. Fill in a table of the 9 subnets: name, tier, AZ, CIDR. Use `/24` subnets that do not overlap, and choose a pattern that is easy to read.
2. How many addresses can servers use in one `/24`? How many in the whole app tier?
3. Later the app tier needs up to 1,000 servers **in each AZ**. Which prefix (for example `/23`, `/22`, `/21`) does each app subnet need? Show your calculation, and remember the 5 addresses AWS keeps.
4. For each tier, write what its route table contains: only `local`, or also `0.0.0.0/0`, and to what (internet gateway or NAT gateway)?

## Part D: Five design questions (30 points)

Answer each in 4 to 6 sentences, in your own words. These are written like Solutions Architect exam questions: say what you would choose **and why the other options are worse**.

1. Your logs show attacks from one IP address, `203.0.113.50`, against every server in a public subnet. You want to block that one address for the whole subnet. Do you use a security group or a network ACL? Why can the other one not do it?
2. Servers in a private subnet must download Linux updates from the internet, and also read files from S3. Name the AWS component for each need. Which of the two costs money every hour, and how can you make the S3 traffic free?
3. Two problems with the shop's RDS database: (a) every evening the reporting team runs heavy reports and the shop becomes slow; (b) the manager asks what happens if the database's AZ fails. Which RDS feature solves (a), and which solves (b)? Can one feature solve both?
4. The **AWS Well-Architected Framework** has six pillars. Choose **three**. For each, name one thing your Lab 19 to 22 designs already do well, and one thing that is still missing.
5. The shop must be running again within **one hour** if the whole Frankfurt region fails, and may lose at most **15 minutes** of orders. The budget is small. Which of the four disaster recovery strategies (backup and restore, pilot light, warm standby, multi-site active-active) do you choose? Explain what **RTO** and **RPO** mean using this example.

## Submit in Google Classroom

- the output from Part A
- `vpc.sh`, `vpc-delete.sh`, the screenshot and the delete output from Part B
- your table and answers from Part C
- your answers to Part D

---

Previous: [Lab 22](lab-22-database-in-a-private-subnet.md) · [All labs](../README.md)
