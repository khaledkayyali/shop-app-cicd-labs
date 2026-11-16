# Lab 20: Self-healing, scaling and cleanup

**Session 7 · 30 minutes · EC2 and CloudWatch consoles**

**Goal:** break a server and watch AWS replace it, add a server by changing one number, see the alarms a scaling policy creates, and delete everything in the right order.

**Words you will need:** self-healing · scale out · scale in · target tracking · CloudWatch alarm

You need everything from Lab 19, still running.

## Step 1: Break a server on purpose

1. **Instances**: select **one** `<yourname>-app` server. **Instance state**, **Terminate (delete) instance**. Write down the time.
2. **Auto Scaling Groups**, `<yourname>-asg`, tab **Activity**. Refresh every 30 seconds.
3. **Target Groups**, `<yourname>-tg`, **Targets**: watch the old target go away and a new one become **healthy**.
4. Keep refreshing your page at the load balancer's address the whole time.

**Question 45:** how many minutes until two targets were healthy again? What does the Activity history say about why the new server started? Did your page stop working at any moment?

## Step 2: Scale out by hand

1. `<yourname>-asg`, tab **Details**, **Group details**, **Edit**. Set **Desired capacity** to **3**. Update.
2. Wait until three targets are healthy, then refresh your page until you have seen three different instance IDs.

**Question 46:** in which Availability Zone did the third server start? Why there?

## Step 3: A scaling policy

1. `<yourname>-asg`, tab **Automatic scaling**, **Create dynamic scaling policy**.
2. Policy type **Target tracking scaling**, metric **Average CPU utilization**, target value **50**. Create.
3. Open **CloudWatch**, **Alarms**, **All alarms**. Find the two alarms with your group's name in them, and open each one.

**Question 47:** what are the two alarms, what condition does each one watch, and what does each one make the group do? With almost no visitors, what will the group do with your three servers after some minutes?

## Step 4: Clean up, in this order

The order matters: a group starts new servers if you delete them first, and a security group cannot be deleted while something still uses it.

1. **Auto Scaling Groups**: select `<yourname>-asg`, **Actions**, **Delete**, type `delete`. This also deletes its servers and its scaling policy. Wait until the group is gone and `<yourname>-app` servers show **Terminated**.
2. **Load Balancers**: delete `<yourname>-alb`.
3. **Target Groups**: delete `<yourname>-tg`.
4. **Launch Templates**: delete `<yourname>-lt`.
5. **Security Groups**: delete `<yourname>-app-sg` first, then `<yourname>-alb-sg`. If one refuses, wait a minute: the load balancer may still be shutting down.
6. Check in CloudShell that nothing of yours is left:

```bash
ME=sara   # your first name
aws autoscaling describe-auto-scaling-groups --query "AutoScalingGroups[?starts_with(AutoScalingGroupName, '$ME')].AutoScalingGroupName"
aws elbv2 describe-load-balancers --query "LoadBalancers[?starts_with(LoadBalancerName, '$ME')].LoadBalancerName"
aws ec2 describe-instances --filters "Name=tag:Name,Values=$ME-*" "Name=instance-state-name,Values=pending,running,stopped" --query 'Reservations[].Instances[].InstanceId'
```

Every command must print `[]`.

**Question 48:** which of today's resources cost money every hour they exist, even with no visitors? Why must the Auto Scaling group be deleted before its servers?

## Done when

All three commands print `[]`, and you can answer Questions 45 to 48.

## Submit in Google Classroom

Your answers to Questions 40 to 48.

---

Previous: [Lab 19](lab-19-load-balancer-and-auto-scaling.md) · Next: [Homework 7](homework-07.md) · [All labs](../README.md)
