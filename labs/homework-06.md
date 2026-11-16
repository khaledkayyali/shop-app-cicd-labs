# Homework 6: The same server, from the command line

**After Session 6 · before Session 7 · about 2.5 hours · 100 points**

In class you clicked through the console. DevOps engineers automate, so here you build the same web server with commands only, draw where it lives, and check that you left nothing running.

**Before you start:** sign in, choose **eu-central-1**, and open CloudShell. Every name starts with your first name. Delete everything at the end.

## Part A: Labs 15 to 17 (15 points)

Labs 15 to 17 are done and your Questions 31 to 39 are answered. In CloudShell, run this and paste the output into your answers. It must show no running instance and no bucket of yours:

```bash
aws ec2 describe-instances --filters "Name=tag:Name,Values=$ME-*" \
  "Name=instance-state-name,Values=pending,running,stopping,stopped" \
  --query 'Reservations[].Instances[].InstanceId'
aws s3 ls | grep "$ME" || echo "no buckets"
```

## Part B: A web server with the AWS CLI (40 points)

In CloudShell, with commands only (no console clicks), build what you built in Lab 17. Save every command you run in a file called `launch.sh`, so you can run the whole thing again later.

1. Save the user data script from Lab 17 to a file called `web.sh`.
2. Find the ID of the default VPC. *Hint: `aws ec2 describe-vpcs` with a filter on `is-default`.*
3. Create a security group `<yourname>-cli-sg` in that VPC, and allow TCP port 80 from `0.0.0.0/0`. *Hint: `create-security-group`, then `authorize-security-group-ingress`. Save the group ID in a variable.*
4. Start one `t3.micro` with your user data and the tag `Name=<yourname>-cli-web`. For the image, use the latest Amazon Linux 2023 through this AWS name: `resolve:ssm:/aws/service/ami-amazon-linux-latest/al2023-ami-kernel-default-x86_64`. *Hint: `run-instances` with `--image-id`, `--instance-type`, `--security-group-ids`, `--user-data file://web.sh` and `--tag-specifications`.*
5. Wait until it runs (`aws ec2 wait instance-running`), print its public IP, and open the page in your browser.
6. Terminate it, wait until it is terminated, and delete the security group, all with commands.

Submit `launch.sh` and a screenshot of your page.

## Part C: Draw it (20 points)

Draw where your Lab 17 server lived, with any free tool (draw.io, Excalidraw, or paper and a photo). Show: the region, two Availability Zones, the default VPC, the subnet your server used, the security group with its rule, the server, the internet, and your S3 bucket from Lab 16. Under the drawing, write three sentences explaining the path of a request from your browser to nginx.

## Part D: Four short questions (25 points)

Answer each in 3 to 6 sentences, in your own words.

1. Explain IaaS, PaaS and SaaS. Give one example of each, and say which one we used in Lab 17 and which one GitHub Pages is.
2. A company runs shop-app on one server in one Availability Zone. What happens when that AZ has a power failure? What would you change?
3. What is the difference between an IAM user, a group, a policy and a role? Which of them did you meet today?
4. List four things you used in Session 6 that cost money, and four habits that protect a company from a surprise bill.

## Submit in Google Classroom

- the output from Part A
- `launch.sh` and the screenshot from Part B
- your drawing from Part C
- your answers to Part D

---

Previous: [Lab 17](lab-17-your-first-server.md) · Next session: [Lab 18](lab-18-inside-the-server.md) · [All labs](../README.md)
