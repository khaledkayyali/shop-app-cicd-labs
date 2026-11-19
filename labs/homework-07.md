# Homework 7: Update without downtime

**After Session 7 · before Session 8 · about 2.5 hours · 100 points**

Real teams change their servers while customers keep shopping. Here you roll out a new version to an Auto Scaling group with no downtime, write a script that finds anything you forgot to delete, and explain the design.

**Before you start:** sign in, choose **eu-central-1**. Every name starts with your first name. Delete everything at the end: the load balancer costs money every hour it exists.

## Part A: Labs 18 to 20 (15 points)

Labs 18 to 20 are done, and your Questions 40 to 48 are answered. Paste the output of the three check commands from Lab 20, Step 4.

## Part B: A rolling update (35 points)

1. Build Lab 19 again, Steps 1 to 5. It is faster the second time; time yourself.
2. Open your page at the load balancer's address. It shows version 1.
3. **Launch Templates**, `<yourname>-lt`, **Actions**, **Modify template (Create new version)**. Change only the user data: the `<h1>` line becomes `<h1>Shop App on AWS, version 2</h1>`. Create the new version.
4. Your group uses version **Latest**, so new servers get version 2. Old servers still run version 1.
5. `<yourname>-asg`, tab **Instance refresh**, **Start instance refresh**. Minimum healthy percentage **50**. Start.
6. Refresh your page every 20 seconds until the refresh finishes. Watch the **Instance refresh** tab too.
7. Delete everything in the order from Lab 20, Step 4.

Submit: two screenshots (one showing version 1 and version 2 answering during the refresh, one of the finished refresh), and three sentences: what did the group do, step by step, and why did your page never go down?

## Part C: "What did I forget?" (25 points)

Write a script `my-resources.sh` for CloudShell that prints everything of yours, by name, that can cost money. It takes your first name from the variable `ME` and prints, under a heading for each:

1. instances that are not terminated
2. EBS volumes
3. snapshots owned by the account
4. load balancers
5. target groups
6. Auto Scaling groups
7. launch templates
8. security groups (except `default`)
9. S3 buckets

*Hints: Lab 20, Step 4 already has three of these. Instances and volumes can filter on the `Name` tag (`--filters "Name=tag:Name,Values=$ME-*"`). For the others use `--query` with `starts_with(..., '$ME')`. The S3 list is `aws s3api list-buckets`.*

Run it while your Part B resources exist and again after cleanup. Submit the script and both outputs.

## Part D: Four short questions (25 points)

Answer each in 3 to 6 sentences, in your own words.

1. What is the difference between **vertical** and **horizontal** scaling? Which one did Lab 20 use, and why do cloud teams prefer it?
2. An Auto Scaling group can use **EC2 health checks** or **Elastic Load Balancing health checks**. nginx crashes but Linux keeps running: which check notices it, and what happens next?
3. **EBS**, **S3** or **instance store**: which would you use for (a) product photos for the website, (b) the disk of a database server, (c) a temporary cache you can lose? Explain each choice.
4. Shop-app now runs on two servers in two AZs behind a load balancer. One whole AZ goes down. Describe what happens, minute by minute. What is still missing before shop-app is truly reliable?

## Submit in Google Classroom

- the output from Part A
- the screenshots and sentences from Part B
- `my-resources.sh` and its two outputs from Part C
- your answers to Part D

---

Previous: [Lab 20](lab-20-self-healing-and-cleanup.md) · Next session: [Lab 21](lab-21-your-own-vpc.md) · [All labs](../README.md)
